---
title: "Dockerのボリュームマウントを使ってMySQLのデータベースコンテナを作成する"
date: "2020-01-31T01:01:01.284Z"
category: "dev"
description: "Dockerのボリュームマウントを使ってMySQLのデータベースコンテナを作成する"
emoji: "🐀"
---

ボリュームマウントとは、ホスト上のディレクトリではなく、Docker Engine 上で確保した領域をマウントする方法です。
ボリュームは`docker vokume create`コマンドを使って作成します。

データベースのデータはボリュームマウントを使って保存するのに向いています。
Docker が扱うデータをブラックボックスとし、コンテナを破棄してもデータが残るようにできるからです。

## ボリュームを作成する

```bash
$ docker volume create mysqlvolume
mysqlvolume
$ docker volume ls
DRIVER    VOLUME NAME
local     mysqlvolume
```

## ボリュームマウントしたコンテナを作成する

```bash
$ docker run --name db01 -dit -v mysqlvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql:5.7

$ docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED          STATUS          PORTS                 NAMES
08b6467633c4   mysql:5.7   "docker-entrypoint.s…"   15 seconds ago   Up 14 seconds   3306/tcp, 33060/tcp   db01
```

### コンテナ内に入ってデータベースを作成する

```bash
$ docker exec -it db01 /bin/bash
root@08b6467633c4:/# mysql -p
Enter password:

mysql> CREATE DATABASE exampledb;
Query OK, 1 row affected (0.00 sec)

mysql> use exampledb;
Database changed

mysql> CREATE TABLE exampletable (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(50), PRIMARY KEY(id));
Query OK, 0 rows affected (0.01 sec)

mysql> INSERT INTO exampletable (name) VALUES ('user01');
Query OK, 1 row affected (0.02 sec)

mysql> INSERT INTO exampletable (name) VALUES ('user02');
Query OK, 1 row affected (0.00 sec)

mysql> SELECT * FROM exampletable;
+----+--------+
| id | name   |
+----+--------+
|  1 | user01 |
|  2 | user02 |
+----+--------+
2 rows in set (0.00 sec)

mysql> \q
Bye
```

### コンテナを破棄する

```bash
root@08b6467633c4:/# exit
exit
ubuntu@ip-10-0-0-235:~$ docker stop db01
db01
ubuntu@ip-10-0-0-235:~$ docker rm db01
db01
```

### 新しいコンテナを作ってみる

```bash
$ docker run --name newdb -dit -v mysqlvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql:5.7
859ac8a94df5f668952d10762eba98478e07e0861ae9719cbac226a5592a2e1f

$ docker exec -it newdb /bin/bash
root@859ac8a94df5:/#
root@859ac8a94df5:/# mysql -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 2
Server version: 5.7.33 MySQL Community Server (GPL)

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> use exampledb;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> SELECT * FROM exampletable;
+----+--------+
| id | name   |
+----+--------+
|  1 | user01 |
|  2 | user02 |
+----+--------+

```

最初のコンテナを破棄して新たにコンテナを作り直しても、テーブルの情報は保持されていることがわかりました。

### 後始末

```bash
$ docker stop newdb
newdb
ubuntu@ip-10-0-0-235:~$ docker rm newdb
newdb
```

## ボリュームマウントで保存したデータのバックアップ

### ボリュームの場所

ボリュームの詳細情報は`docker volume inspect`コマンドで確認できます。

```bash
$ docker volume inspect mysqlvolume
[
    {
        "CreatedAt": "2021-01-31T02:27:24Z",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mysqlvolume/_data",
        "Name": "mysqlvolume",
        "Options": {},
        "Scope": "local"
    }
```

### ボリュームをバックアップする

`"Mountpoint": "/var/lib/docker/volumes/mysqlvolume/_data"`は Docker Engine のシステム領域であるため、ここのファイルをホストからバックアップしても正しくバックアップされるとは限りません。

Docker でボリュームをバックアップするときは、適当なコンテナを割り当てて、そのコンテナを使ってバックアップを取るようにします。

```bash
$ docker run --rm -v mysqlvolume:/src -v "$PWD":/dest busybox tar czvf /dest/backup.tar.gz -C /src .

$ ls -al
-rw-r--r-- 1 root   root   7.0M Jan 31 02:39 backup.tar.gz
$ tar tzvf backup.tar.gz
drwxrwxrwt 999/999           0 2021-01-31 02:27 ./
drwxr-x--- 999/999           0 2021-01-31 02:09 ./sys/
-rw-r----- 999/999        2430 2021-01-31 02:09 ./sys/x@0024innodb_buffer_stats_by_schema.frm
```

### ボリュームを削除する

```bash
$ docker volume rm mysqlvolume
mysqlvolume

$ docker volume ls
DRIVER    VOLUME NAME
```

### ボリュームをリストアする

```bash

```
