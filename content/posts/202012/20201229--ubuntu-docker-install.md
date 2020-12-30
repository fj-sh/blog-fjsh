---
title: "UbuntuにDockerをインストールする"
date: "2020-12-29T01:00:00.001Z"
template: "post"
draft: false
slug: "ubuntu-docker-install"
category: "docker"
tags:
  - "Docker"
description: "UbuntuにDockerをインストールする手順"
---

AWSに立てたUbuntuのEC2インスタンス上にDockerをインストールする手順です。

## Docker Engineをインストールする

[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

Ubuntuのパッケージをアップデートします。

```
sudo apt-get update
```

DockerオフィシャルサイトのGPGキーをダウンロードし、登録します。

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Dockerダウンロードサイトをaptリポジトリに追加します。

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Docker Engine一式をインストールします。

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

ubuntuユーザーでdockerを利用できるようにします。
```
sudo gpasswd -a ubuntu docker
```

一度ログアウトして再ログインします。
```
docker --version
Docker version 20.10.1, build 831ebea
```

でインストール完了です。


## Dockerでウェブサーバを起動する

Dockerを立ち上げたついでに、そのままウェブサーバを作ってみましょう。
Dockerfileなしで稼働させるパターンを見てみます。


### Dockerイメージを探す

[Docker Hub](https://hub.docker.com/)の検索ボックスに「apache」と入力します。

すると「httpd」と名付けられたDocker Official Imagesが表示されます。

イメージの使い方が説明されているので、そこに書いてある「Dockerfileなしでイメージを利用する場合」のコマンドを使ってみます。

```
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```


### 実行状態の確認

`docker ps`コマンドで実行状態を確認できます。

```
$ docker ps
CONTAINER ID   IMAGE       COMMAND              CREATED          STATUS          PORTS                  NAMES
3ee8c2102f91   httpd:2.4   "httpd-foreground"   25 seconds ago   Up 24 seconds   0.0.0.0:8080->80/tcp   my-apache-app
```

`http://EC2インスタンスのIPアドレス:8080/`とブラウザのアドレス欄に入力すると、Apacheのウェブページを表示することができます。


### コンテナの停止

`docker stop コンテナ名`でコンテナを停止させることができます。

起動時に`--name`で指定した値がコンテナ名です。コンテナ名の代わりに`docker ps`で表示されるコンテナIDを指定することもできます。

```
$ docker stop my-apache-app
my-apache-app
```

### ログを確認する

以下のコマンドで実行中のログが確認できます。

```
docker logs my-apache-app
```

### コンテナの破棄

コンテナを破棄するコマンドは`docker rm`です。
```
docker rm my-apache-app
```


### イメージの破棄

イメージを破棄するコマンドは`docker image rm イメージ名`です。

```
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
httpd        2.4       dd85cdbb9987   2 weeks ago   138MB
```

イメージ名は`docker image ls`で表示された「REPOSITORY」と「TAG」を「:（半角のコロン）」でつなげたものです。

```
$ docker image rm httpd:2.4
Untagged: httpd:2.4
Untagged: httpd@sha256:a3a2886ec250194804974932eaf4a4ba2b77c4e7d551ddb63b01068bf70f4120
Deleted: sha256:dd85cdbb99877b73f0de2053f225af590ab188d79469eebdb23ec2d26d0d10e8
Deleted: sha256:bc2085990715d2d1ac2179131969b293821dbcf7f0538eaeb3bc3bb6d5645a13
Deleted: sha256:a11bf2367d443697ca6267194a18b31289b59b677133e6482779851fa33992df
Deleted: sha256:388a801aa9f7953143fc154e7bc16dd5b696d2f3578a054b0b804557a8d89d74
Deleted: sha256:37495edb13f558e001fba79f653b38ac3cad5e29f189c1e20501f3f6d5326fdc
Deleted: sha256:87c8a1d8f54f3aa4e05569e8919397b65056aa71cdf48b7f061432c98475eee9

$ docker image ls
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```