---
title: "Rubyの基礎チートシート"
date: "2020-12-15T22:00:00.001Z"
template: "post"
draft: false
slug: "basic-ruby"
category: "Ruby"
tags:
  - "Ruby"
description: "サクッと調べるRubyの書き方"
---

Rubyの書き方で困ったときに参照するためのページです。

## if文

```ruby
n = 11

if n > 10
    puts '10より大きい'
else
    puts '10以下'
end

country = 'italy'
if country == 'japan'
    puts 'こんにちは'
elsif country == 'us'
    puts 'Hello'
elsif  country == 'italy'
    puts 'ciao'
else
    puts '???'
end
```

## メソッドを定義する

```ruby

def add(a, b)
    a + b
end

add(1, 2)

def greeting(country)
    if country == 'japan'
        'こんにちは'
    else
        'hello'
    end
end

greeting('japan')
greeting('us')
```

### デフォルト値付きの引数

```ruby
def greeting(country = 'japan')
    if country == 'japan'
        'こんにちは'
    else
        'hello'
    end
end

puts (greeting) # こんにちは
puts (greeting('us')) # hello


def call_time(time = Time.now, message = greeting)
    puts "time: #{time}, message: #{message}"
end

call_time  # time: 2020-12-16 07:48:40 +0900, message: こんにちは
```

- `?`で終わるメソッドは慣習として真偽値を返す
  - `''.empty?`
  - `'watch'.include?('at')`
- `!`で終わるメソッドは「使用する際は注意が必要」という意味
  - 呼び出したオブジェクトの状態を変更してしまうメソッドのことを「破壊的メソッド」と呼ぶ
  - 破壊的メソッドがすべて`!`で終わるわけではないが、`!`で終わるメソッドには何かしらの注意事項があるはず


## FizzBuzz

```ruby

def fizzbuzz(num)
    if num % 15 == 0
        "Fizz Buzz"
    elsif num % 5 == 0
        "Buzz"
    elsif num % 3 == 0
        "Fizz"
    else
        num.to_s
    end
end

puts(fizzbuzz(30))  # Fizz Buzz
puts(fizzbuzz(5)) # Buzz
puts(fizzbuzz(3)) # Fizz
puts(fizzbuzz(7)) #7
```

## 繰り返し

## 配列

## ハッシュ

## シンボル


## 文字列

- 文字列中に改行文字（`\n`）を埋め込みたい場合は、ダブルクオートで囲む必要がある
- シングルクオートを使う場合は式展開されない

### %記法で文字列を作る

### ヒアドキュメント

### フォーマットを指定して文字列を作成

### 数値を文字列に変換する

### 配列を連結して1つの文字列にする


## 数値

### 指数表現






## その他

### 式（Expression）と文（Statement）

- 値を返し、結果を変数に代入できるものが式
- 値を返さず、変数に代入しようとすると構文エラーになるものが文

文ではなく式になっているif文の例：
```ruby
a = 
  if true
    '真です'
  else
    '偽です'
  end

puts (a)

b = def foo; end
puts (b) 
```

### 疑似変数

- `nil`と`true`と`false`は疑似変数
- `self`：オブジェクト自身
- `__FILE__`：現在のソースファイル名
- `__LINE__`：現在のソースファイル中の行番号
- `__ENCODING__`：現在のソースファイルのスクリプトエンコーディング


### require

- 組み込みライブラリでない標準ライブラリやgemを利用する場合は、明示的にライブラリを読み込む必要がある。


```ruby
require 'date'
puts(Date.today) # 2020-12-16
```

自分で作成したRubyプログラムを読み込む場合も`require`を使う。
- 拡張子を含む場合：`require './sample.rb'`
- 拡張子を含まなくてもOK：`require './sample'`

### load

- `require`は1回しかライブラリやrbファイルを読み込まない
- 無条件に指定したファイルを読み込みたい場合は`load`を使う
  - `load`を使う場合は拡張子の`.rb`を省略できない

### require_relative

- `require_relative`を使うと自分のファイルが存在するディレクトリがパスの起点になる


## ターミナルへの標準出力

- `puts`：改行を加えて変数の内容やメソッドの戻り値をターミナルに出力する
- `print`：改行を加えずに変数の内容やメソッドの戻り値をターミナルに出力する
- `p`：改行を加えてターミナルに出力する。文字列を出力すると、その文字列がダブルクオートで囲まれる。
  - `p`は`\n`などの改行文字が改行文字のまま出力される
ー `puts`と`print`は一般ユーザー向け、`p`は開発者向けの用途で作られている

