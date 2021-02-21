---
title: "Dockerでイメージを自作する"
date: "2020-02-08T01:01:01.284Z"
category: "dev"
description: "Dockerでカスタムイメージを作成する"
emoji: "🖖"
---

Dockerでイメージを自作する方法について記載していきます。

イメージを自作する利点は下記です。

- コンテナ起動後の操作が必要ないため、Dockerに詳しくなくても利用できる
- 設定や調整のミスを排除できる
- システムの稼働に必要なライブラリやデータを含めることができる
- イメージを作成する際に、操作の履歴を残すことができる



## Dockerfileからイメージを作る

1. 作業用ディレクトリでDockerfileを作る
2. イメージをビルドする
3. ビルドしたイメージを利用する
4. docker saveでイメージからファイル化する

### 作業用ディレクトリでDockerfileを作る

```dockerfile
FROM httpd
COPY index.html /usr/local/apache2/htdocs
```

### イメージをビルドする

```shell
$ docker build -t myimage01 .

$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myimage01    latest    3d946aefb505   18 seconds ago   138MB
```

### ビルドしたイメージを利用する

```shell
$ docker run -dit --name webcontent_docker -p 8080:80 myimage01
f5b7c10a16a6c99a8dfcc9c465357d4e922ea62e02efff5072e39def6f19c295
```

### docker saveでイメージからファイル化する

```shell
$ docker save -o saved.tar myphpimage
$ ls -al saved.tar
$ tar tvf saved.tar 
```

### docker loadする

```shell
$ docker load -i saved.tar
$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
myphpimage   latest    dcc3f466b92a   27 minutes ago   245MB

$ docker run -dit --name myphp -p 8080:80 myphpimage

```


## Docker hubにイメージを登録する

Dockerイメージ名はDocker Hubに登録された`<Docker ID>/<リポジトリ名>`に合わせるb必要があります。

```shell
$ docker tag myphpimage fjsh/myphpimage
```

```shell
$ docker login
docker push <ID>/<リポジトリ名>
```

## リポジトリに登録したイメージを使う



## コンテナからイメージを作る

1. 下準備
2. コンテナをイメージ化する
3. イメージを確認する


### 下準備

```shell
$ docker run -dit --name webcontent -p 8080:80 httpd:2.4
$ docker cp /tmp/index.html webcontent:/usr/local/apache2/htdocs
```

### コンテナをイメージ化する

```shell
$ docker commit webcontent mycustomed_httpd
sha256:19654050645025092e7c11265f0489cf77b01ab901aeb85126047406a4262048
```

### イメージを確認する

```shell
$ docker image ls
```

### カスタマイズされたイメージを使う

1. コンテナを作る
2. コンテナを確認する
3. 後始末

### コンテナを作る

```shell
$ docker run -dit --name webcontent_new -p 8081:80 mycustomed_httpd
```


### コンテナを確認する

```shell
$ docker ps
CONTAINER ID   IMAGE              COMMAND              CREATED          STATUS          PORTS                  NAMES
c28bcd538d2d   mycustomed_httpd   "httpd-foreground"   29 seconds ago   Up 28 seconds   0.0.0.0:8081->80/tcp   webcontent_new
0d00e79ec395   httpd:2.4          "httpd-foreground"   5 minutes ago    Up 5 minutes    0.0.0.0:8080->80/tcp   webcontent

$ docker exec -it webcontent_new /bin/bash
root@c28bcd538d2d:/usr/local/apache2# cat /usr/local/apache2/htdocs/index.html 
<html>
<body>
<div>Docker Contents</div>
</body>
</html>

```

### 後始末

```shell
$ docker stop webcontent webcontent_new
$ docker rm webcontent webcontent_new
$ docker image rm mycustomed_httpd
```



