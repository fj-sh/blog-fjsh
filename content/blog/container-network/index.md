---
title: "Dockerのコンテナ間で通信を行う"
date: "2020-02-01T01:01:01.284Z"
category: "dev"
description: "Dockerコンテナのネットワークについて"
emoji: "🐀"
---

Docker ホスト上にはたくさんのコンテナを実行できます。
それぞれ独立していますが、コンテナ間で通信が必要となるときがあります。

Docker コンテナのネットワークについて知っておく必要があります。

## Docker コンテナ間で通信を行う

1. 2 つのコンテナを起動する
2. コンテナの IP アドレスを確認する
3. Docker ホスト同士の通信を行う

### 2 つのコンテナを起動する

```shell
$ docker run -dit --name web01 -p 8080:80 httpd:2.4
$ docker run -dit --name web02 -p 8081:80 httpd:2.4
```

### コンテナの IP アドレスを確認する

```shell
$ docker container inspect web01 | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

#### --format オプションを指定する場合

```shell
$ docker container inspect --format="{{.NetworkSettings.IPAddress}}" web02
172.17.0.3
```

### Docker ホスト同士の通信を行う

DOcker ホスト同士で通信を行ってみます。

1. 通信テスト用のコンテナを用意する
2. 自身の IP アドレスを確認する
3. ping で疎通確認する
4. コンテンツを取得する

#### 通信テスト用のコンテナを用意する

```shell
$ docker run --rm -it ubuntu /bin/bash
root@ce7a7ddb7e41:/# apt update
root@ce7a7ddb7e41:/# apt -y upgrade
root@ce7a7ddb7e41:/# apt install -y iproute2 iputils-ping curl
```

#### 自身の IP アドレスを確認する

```shell
root@ce7a7ddb7e41:/# ip address
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
8: eth0@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:11:00:04 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.17.0.4/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

#### ping で疎通確認する

```shell
root@ce7a7ddb7e41:/# ping -c 2 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.049 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.042 ms

--- 172.17.0.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1012ms
rtt min/avg/max/mdev = 0.042/0.045/0.049/0.003 ms

root@ce7a7ddb7e41:/# ping -c 2 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.081 ms
64 bytes from 172.17.0.3: icmp_seq=2 ttl=64 time=0.059 ms

--- 172.17.0.3 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1026ms
rtt min/avg/max/mdev = 0.059/0.070/0.081/0.011 ms
```

#### コンテンツを取得する

```shell
root@ce7a7ddb7e41:/# curl http://172.17.0.3
<html><body><h1>It works!</h1></body></html>
```

### Docker ネットワークを作成する

1. Docker ネットワークを作成する
2. 作成されたネットワークを確認する
3. IP アドレスの設定を確認する

#### Docker ネットワークを作成する

```shell
$ docker network create mydockernet
```

#### 作成されたネットワークを確認する

```shell
$ docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
18695d9af649   bridge        bridge    local
82acd9c81ab9   host          host      local
24e16d544ffa   mydockernet   bridge    local
```

#### IP アドレスの設定を確認する

```shell
$ docker network inspect mydockernet
[
    {
        "Name": "mydockernet",
        "Id": "24e16d544ffa849dda5349a287847761b3444127f8f3790ecc7cf6e3c64905d5",
        "Created": "2021-02-01T02:45:26.354498781Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

### Docker ネットワークにコンテナを作る

1. 作成したネットワークに接続してコンテナを作成
2.

#### 作成したネットワークに接続してコンテナを作成

```shell
$ docker run -dit --name web01 -p 8080:80 --net mydockernet httpd:2.4
$ docker run -dit --name web02 -p 8081:80 --net mydockernet httpd:2.4
```

#### ネットワーク接続を確認する

```shell
$ docker network inspect mydockernet
```
