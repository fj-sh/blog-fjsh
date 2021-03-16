---
title: "Docker Composeで複数のコンテナをまとめて起動する"
date: "2020-02-02T01:01:01.284Z"
category: "dev"
description: "Docker Composeを使ってみよう"
emoji: "🖖"
---

Docker Compose は複数のコンテナをまとめて起動したり停止できる仕組みです。

Docker Compose を使って複数のコンテナをひとまとめにして操作する方法を見ていきましょう。

はじめに Docker を使って WordPress サイトを構築してみます。
以下の作業を行います。

1. Docker ネットワークを作成する
2. ボリュームを作る
3. MySQL コンテナを作る
4. WordPress コンテナを作る

次に上記の作業を Docker Compose を使ってまとめて実行してみます。

## Docker ネットワークを作成する

```shell
$ docker network create wordpressnet

$ docker network ls
NETWORK ID     NAME           DRIVER    SCOPE
18695d9af649   bridge         bridge    local
82acd9c81ab9   host           host      local
e9f708dec077   none           null      local
4a8e13e8b48d   wordpressnet   bridge    local
```

## MySQL データベースの永続化に用いるボリュームを作成する

```shell
$ docker volume create wordpress_db_volume

$ docker volume ls
DRIVER    VOLUME NAME
local     wordpress_db_volume
```

### データベースの初期値に設定する環境変数

| 環境変数名            | 意味                               | サンプルの設定値 |
| --------------------- | ---------------------------------- | ---------------- |
| `MYSQL_ROOT_PASSWORD` | 管理者パスワード                   | myrootpassword   |
| `MYSQL_DATABASE`      | 起動と同時に作成するデータベース名 | wordpressdb      |
| `MYSQL_USER`          | データベースに接続できるユーザー名 | wordpressuser    |
| `MYSQL_PASSWORD`      | ユーザーに対するパスワード         | wordpresspass    |

## MySQL コンテナを起動する

```shell
$ docker run --name wordpress-db -dit -v wordpress_db_volume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=myrootpassword -e MYSQL_DATABASE=wordpressdb -e MYSQL_USER=wordpressuser -e MYSQL_PASSWORD=wordpresspass --net wordpressnet mysql:5.7

$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                 NAMES
93a97fa37a37   mysql:5.7   "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   3306/tcp, 33060/tcp   wordpress-db
```

## WordPress コンテナを起動する

| 環境変数名              | 意味                               | サンプルの設定値 |
| ----------------------- | ---------------------------------- | ---------------- |
| `WORDPRESS_DB_HOST`     | 接続データベースのホスト名         | wordpress-db     |
| `WORDPRESS_DB_NAME`     | 接続データベース名                 | wrodpressdb      |
| `WORDPRESS_DB_USER`     | データベースに接続できるユーザー名 | wordpressuser    |
| `WORDPRESS_DB_PASSWORD` | ユーザーに対するパスワード         | wordpresspass    |

```shell
$ docker run --name wordpress-app -dit -p 8080:80 -e WORDPRESS_DB_HOST=wordpress-db -e WORDPRESS_DB_NAME=wordpressdb -e WORDPRESS_DB_USER=wordpressuser -e WORDPRESS_DB_PASSWORD=wordpresspass --net wordpressnet wordpress

$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED              STATUS              PORTS                  NAMES
23a486e6edf0   wordpress   "docker-entrypoint.s…"   13 seconds ago       Up 11 seconds       0.0.0.0:8080->80/tcp   wordpress-app
93a97fa37a37   mysql:5.7   "docker-entrypoint.s…"   About a minute ago   Up About a minute   3306/tcp, 33060/tcp    wordpress-db
```

`http://<EC2インスタンスのIPアドレス>:8080/`で WordPress の設定画面を開くことができます。

## 後始末

```shell
$ docker stop wordpress-db wordpress-app
$ docker rm wordpress-db wordpress-app
$ docker network rm wordpressnet
$ docker volume rm wordpress_db_volume
```

## Docker Compose

1. Docker Compose のインストール
2. docker-compose.yml の準備

### Docker Compose のインストール

```shell
$ sudo apt install -y python3 python3-pip
$ sudo pip3 install docker-compose
$ docker-compose --version
docker-compose version 1.28.2, build unknown
```

### docker-compose.yml の準備

Docker Compose は`docker-compose.yml`という設定ファイルにコマンドの内容をまとめます。

```yaml
version: "3"

services:
  wordpress-db:
    image: mysql:5.7
    networks:
      - wordpressnet
    volumes:
      - wordpress_db_volume:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: myrootpassword
      MYSQL_DATABASE: wordpressdb
      MYSQL_USER: wordpressuser
      MYSQL_PASSWORD: wordpresspass

  wordpress-app:
    depends_on:
      - wordpress-db
    image: wordpress
    networks:
      - wordpressnet
    ports:
      - 8080:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: wordpress-db
      WORDPRESS_DB_NAME: wordpressdb
      WORDPRESS_DB_USER: wordpressuser
      WORDPRESS_DB_PASSWORD: wordpresspass

networks:
  wordpressnet:

volumes:
  wordpress_db_volume:
```

- サービス
  - 全体を構成する 1 つひとつのコンテナのこと
- ネットワーク
  - サービスが参加するネットワークを定義する
- ボリューム
  - サービスが利用するボリュームを定義する

### Docker Compose でコンテナを起動する

```shell
$ docker-compose up -d

$ docker-compose ps
          Name                         Command               State          Ports
-----------------------------------------------------------------------------------------
wordpress_wordpress-app_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:8080->80/tcp
wordpress_wordpress-db_1    docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp
```

### コンテナを停止して破棄する

```shell
$ docker-compose down
Stopping wordpress_wordpress-app_1 ... done
Stopping wordpress_wordpress-db_1  ... done
Removing wordpress_wordpress-app_1 ... done
Removing wordpress_wordpress-db_1  ... done
Removing network wordpress_wordpressnet

$ docker-compose ps
Name   Command   State   Ports
------------------------------

$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
18695d9af649   bridge    bridge    local
82acd9c81ab9   host      host      local
e9f708dec077   none      null      local

$ docker volume ls
DRIVER    VOLUME NAME
local     wordpress_wordpress_db_volume
```

### docker-compose で一つずつコンテナを操作

```shell
$ docker-compose up -d
$ docker-compose exec wordpress-app /bin/bash
root@e9683c84df1d:/var/www/html# ls
index.php    wp-activate.php	 wp-comments-post.php  wp-content   wp-links-opml.php  wp-mail.php	wp-trackback.php
# exit

$ docker-compose stop wordpress-db
$ docker-compose start wordpress-db
$ docker-compose down
```

## 参考

- [SIer における Kubernetes 活用](https://www.scsk.jp/lib/product/oss/pdf/Kubernetes_1.pdf)
- [THE TWELVE-FACTOR APP](https://12factor.net/ja/)
