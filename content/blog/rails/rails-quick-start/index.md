---
title: "Railsクイックスタートガイド"
date: "2020-03-09T01:01:01.284Z"
category: "dev"
description: "Railsのプロジェクトを始める"
emoji: "😎"
---

Railsで開発を始める際の情報をメモの形でまとめる。

## Railsを動かすためのDocker環境

下記のDockerfileを使ってRailsの環境を作った。


```dockerfile:title=Dockerfile
FROM ruby:2.6.6-alpine3.12

RUN apk update
RUN apk upgrade

RUN apk add --no-cache \
bash git vim openssh openssl yarn sudo su-exec shadow tzdata \
postgresql-client postgresql-dev \
build-base libxml2-dev libxslt-dev

ARG UID=1000
ARG GID=1000

RUN mkdir -p /var/mail
RUN groupadd -g $GID devel
RUN useradd -u $UID -g devel -m devel
RUN echo "devel ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

WORKDIR /tmp
COPY init/Gemfile /tmp/Gemfile
COPY init/Gemfile.lock /tmp/Gemfile.lock
RUN bundle install

COPY ./apps /apps

RUN apk add --no-cache openssl

USER devel

RUN openssl rand -hex 64 > /home/devel/.secret_key_base
RUN echo $'export SECRET_KEY_BASE=$(cat /home/devel/.secret_key_base)' \
  >> /home/devel/.bashrc

WORKDIR /apps
```

```yml:title=docker-compose.yml
version: '3'
services:
  db:
    image: postgres:11.2-alpine
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
  web:
    build: .
    command: /bin/sh
    environment:
      WEBPACKER_DEV_SERVER_HOST: "0.0.0.0"
      RAILS_SERVE_STATIC_FILES: "1"
      EDITOR: "vim"
    volumes:
      - ./apps:/apps
    ports:
      - "3000:3000"
      - "3035:3035"
    depends_on:
      - db
    tty: true
```

### イメージを作成する

```shell
docker-compose build --no-cache
```

### コンテナを起動する

```shell
docker-compose up -d
```

### Rails開発用のコンテナに入る

```shell
docker-compose exec web bash
```


## アプリケーションのひな形を作成する

```
rails new アプリケーション名 [オプション]
```

todolistというアプリケーションのひな形を作成する場合。

```
rails new todolist -d postgresql
```

ひな形作成後はデータベースを作成する。

```
cd todolist
bin/rails db:create
```

データベースは以下の3種類の環境が用意されている。

- 開発 development
- テスト test
- 本番 production

データベースにPostgreSQLを指定した場合（`-d postgresql`）、`config/database.yml`に`host`、`username`、`password`を追加する。

```ruby
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see Rails configuration guide
  # https://guides.rubyonrails.org/configuring.html#database-pooling
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  host: db
  username: postgres
  password:
```

### 不要な機能をスキップしてひな形を作成

```ruby
$ rails new awesome_events -d postgresql --skip-action-mailer --skip-action-mailbox --skip-action-text --skip-action-cable

$ cd awesome_events
$ bin/rails db:create
$ bin/rails db:migrate
```

Railsサーバーを起動する。

```shell
bin/rails s -b 0.0.0.0
```

`http://localhost:3000/`を叩くと、Railsのwelcomeページが表示される。


## Hamlを導入する


Gemfileに以下を追記して`bundle install`

```ruby:title=Gemfile
gem 'hamlit-rails'
```

ERBで書かれたビューをHamlに変換するためのgem、`html2haml`をインストールする。

```shell
bin/rails hamlit:erb2haml
```

変換コマンドを実行したら`html2haml`はGemfileから削除し、`bundle install`を再び実行する。


## ERBの代わりにSlimを使う

Slimを使うためにはGemfileに以下を記述する。

```
gem 'slim-rails'
gem 'html2slim'
```

その後、`bundle install`を実行。



## Railsのエラーメッセージなどを日本語で出せるようにする

```
$ wget https://raw.githubusercontent.com/svenfuchs/rails-i18n/master/rails/locale/ja.yml -P config/locales/
```

## モデルのひな形を作成する

- データベースのテーブル名はモデルのクラス名を複数形にしたもの
- モデルのクラス名はキャメルケース、テーブル名はスネークケース

モデルを作成するためのジェネレータコマンドは以下。

```
bin/rails g model [モデル名] [属性名:データ型 属性名:データ型...]
```

具体例は以下。

```
bin/rails g model Task name:string description:text
bin/rails db:migrate
```

## コントローラー


コントローラを作成するためのジェネレータコマンドは以下。

```
bin/rails g controller コントローラ名 [アクション名 アクション名 ...] [オプション]
```

- アクションとは、リクエストに対しての処理を実行するためのメソッドのこと
- アクションに対する設計あパターン化されている
- URLとHTTPメソッドの組み合わせからリクエストを処理すべきコントローラとアクションを特定する作業を「ルーティング」と呼ぶ

|アクション名|機能名|
| --- | --- |
|index|一覧表示|
|show|詳細表示|
|new|新規登録画面|
|create|登録|
|edit|編集画面|
|update|更新|
|destroy|削除|


index, show, new, editの4つのアクションを指定してジェネレータコマンドを実行する。

```
bin/rails g controller tasks index show new edit
```

するとroutes.rbにアクションに対応するルーティングが設定される。

routes.rbに`resources`を設定すれば、すべてのアクションに関するルーティングを一括で設定してくれる。

```ruby:title=config/routes.rb
Rails.application.routes.draw do
  resources :tasks
end
```

routes.rbに`root to: 'tasks#index'`を設定すれば、ルートパスにアクセスしたときに一覧が表示される。

アクションでブラウザに返す画面を記述を省略した場合、Railsは`app/views/[コントローラ名]/[アクション名].html.erb`を表示する。


### 独自のトップページを表示してみる


```shell
bin/rails g controller welcome index
```

routes.rbを編集する。

```ruby:title=config/routes.rb
Rails.application.routes.draw do
  root "welcome#index"
end
```


## ビュー

ビューの書き方をまとめる。


### Bootstrapの導入

以下のコマンドでBootstrapをインストールする。

```
bin/yarn add bootstrap@4.4.1 jquery@3.5.1 popper.js@1.16.1
```

application.html.hamlにBootstrapのデザインを適用してみる。

```javascript:title=app/javascript/packs/application.js
import "bootstrap"
import "bootstrap/scss/bootstrap.scss"
```

```haml:app/views/layouts/application.html.haml
!!!
%html
  %head
    %meta{:content => "text/html; charset=UTF-8", "http-equiv" => "Content-Type"}/
    %title AwesomeEvents
    = csrf_meta_tags
    = csp_meta_tag
    = stylesheet_link_tag 'application', media: 'all', 'data-turbolinks-track': 'reload'
    = javascript_pack_tag 'application', 'data-turbolinks-track': 'reload'
  %body
    %header.navbar.navbar-expand-sm.navbar-light.bg-light
      .container
        =link_to "AwesomeEvents", root_path, class: "navbar-brand"
    .container
    = yield
```



### link_to

link_toメソッドは`<a href="...">新規登録</a>`のようなリンク先を作成するためのメソッド。

Slimでは`link_to '新規登録', new_task_path, class: 'btn btn-primary'`のように書く。

new_task_pathとはURLヘルパーメソッドで、これを呼び出すことで`/tasks/new`というURL文字列が得られる。



### Flashメッセージ

`redirect_to`には特定のFlashメッセージを渡すことができる。

Flashはリダイレクトの際に、次のリクエストに対してちょっとしたデータを伝えるためにRailsが用意している仕組みのこと。

redirect_toのオプションに直接渡すことができるFlashのキー（名前）はデフォルトでは:noticeと:alertのみ。

