---
title: "Rubyでハッシュのキーの存在を確認、取得（検索）"
date: "2020-12-19T23:00:00.001Z"
template: "post"
draft: false
slug: "ruby-hash-key-value"
category: "Ruby"
tags:
  - "Ruby"
description: "Rubyでハッシュのキーを追加、取得する方法"
---


```ruby
city = { yokohama: 'Kanagawa', sapporo: 'Hokkaido', tatikawa: 'Tokyo'}
city.keys  # [:yokohama, :sapporo, :tatikawa]
city.values  # ["Kanagawa", "Hokkaido", "Tokyo"]
```

## ハッシュの中に指定されたキーが存在するかどうかを確認する

ハッシュの中に指定されたキーが存在するかどうかは`has_key?`で確認できます。
`key?`、`include?`、`member?`がエイリアスメソッドです。

```ruby
city = { yokohama: 'Kanagawa', sapporo: 'Hokkaido', tatikawa: 'Tokyo'}
city.has_key?(:yokohama) # true
city.has_key?(:yamagata) # false
```

