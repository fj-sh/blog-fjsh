---
title: "Rubyのハッシュ、シンボルの使い方"
date: "2020-12-19T23:00:00.001Z"
template: "post"
draft: false
slug: "ruby-hash-symbol"
category: "Ruby"
tags:
  - "Ruby"
description: "ハッシュ、シンボルの使い方のサンプル集です。"
---



ハッシュはキーと値の組み合わせでデータを管理するオブジェクトのこと。


## 要素の追加、変更、取得

要素の追加は`ハッシュ[キー] = 値`の構文を使う。

```ruby
currencies = { 'japan' => 'yen', 'us' => 'dollar', 'india' => 'rupee' }
currencies['italy'] = 'euro'
currencies
# {"japan"=>"yen", "us"=>"dollar", "india"=>"rupee", "italy"=>"euro"}
```

`ハッシュ[キー] = 値`で既存のキーを指定することで要素を上書きすることができる。
```ruby
currencies['japan'] = '円'
```

`ハッシュ[キー]`で値を取り出すことができる。存在しないキーを指定すると`nil`を返す。

```ruby
currencies = { 'japan' => 'yen', 'us' => 'dollar', 'india' => 'rupee' }
currencies['india']  # => "rupee"
```

### シンボル

シンボルは`:シンボルの名前`で定義することができ、以下のような特徴を持ちます。

- 内部的には整数なのでコンピュータは高速に値を比較できる
- 表面上は文字列に似ているので、プログラマにとって理解しやすい
- 同じシンボルであればまったく同じオブジェクトである
- イミュータブルなオブジェクトなので、破壊的な変更はできない

```ruby
currencies = { :japan=>'yen', :us=>'dollar', :india=>'rupee' }
# => {:japan=>"yen", :us=>"dollar", :india=>"rupee"}
currencies[:japan]
# => "yen"
```

## ハッシュを繰り返しで処理する

ハッシュは`each`メソッドでキーと値の組み合わせを順に取り出すことができる。

```ruby
currencies.each do |key, value|
   puts "#{key}: #{value}"
end
# japan: yen
# us: dollar
# india: rupee
```

## ハッシュの要素数の取得、要素の削除

ハッシュのサイズは`size`メソッド（エイリアスメソッドは`length`）で取得できる。
```ruby
currencies.size
# => 3
```

deleteメソッドで指定したキーに対応する要素を削除できる。
```ruby
currencies.delete('japan')
# => "yen"
```