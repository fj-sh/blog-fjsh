---
title: "Rubyの文法チートシート"
date: "2020-12-15T22:00:00.001Z"
template: "post"
draft: false
slug: "ruby-basic"
category: "Ruby"
tags:
  - "Ruby"
description: "Rubyでこれどうやって書くんだっけ？を調べるときに使う記事です。"
---



## if 文

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

## case 文

```ruby
country = 'italy'

case country
when 'japan'
    'こんにちは'
when 'us'
    'Hello'
when 'italy'
    'ciao'
else
    '???'
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

- Ruby は最後に評価された式が戻り地になるのが特徴
- `return`などのキーワードは不要

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
  - 破壊的メソッドがすべて`!`で終わるわけではないが、`!`で終わるメソッドには何かしらの注意事項があるはずと疑う

### メソッドの可変長引数

個数に制限のない引数のことを可変長引数と言う。
可変長引数を使いたい場合は引数名の手前に`*`をつける。

```ruby
def メソッド名(引数1, 引数2, *可変長引数)
  # メソッドの処理
end
```

```ruby
def greeting(*names)
    "#{names.join('と')}、おはようございます"
end

p greeting('田中さん', '鈴木さん', '中島さん') # "田中さんと鈴木さんと中島さん、おはようございます"
```

### メソッドのキーワード引数

メソッドのキーワード引数を定義する構文は以下。

```ruby
def メソッド名(キーワード引数1: デフォルト値1, キーワード引数2: デフォルト値2)
  # メソッドの実装
end
```

```ruby
def buy_burger(menu, drink: true, potato: true)
    if drink
        puts "Buy drink"
    end

    if potato
        puts "Buy potato"
    end
end

buy_burger('cheese', drink:true, potato:true)
```

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

### 添え字付きの繰り返し処理

```ruby
fruits = ['apple', 'orange', 'melon']  # => ["apple", "orange", "melon"]
fruits.each_with_index { |fruit, i| puts "#{i}: #{fruit}" }
# 0: apple
# 1: orange
# 2: melon
```

### with_index を使った添え字付きの繰り返し処理

map メソッドと with_index メソッドを組み合わせて使うこともできる。
なお、map メソッドは各要素に対してブロックを評価した結果を新しい配列にして返す。

```ruby
fruit = ['apple', 'orange', 'melon']  # => ["apple", "orange", "melon"]
fruits.map.with_index { |fruit, i| "#{i}: #{fruit}" }  # => ["0: apple", "1: orange", "2: melon"]
```

`with_index(1)`のように、with_index に引数を渡せば、添字が引数で渡した数値から開始される。

### break で繰り返し処理を脱出する

break を使うと、繰り返し処理を脱出することができる。

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10].shuffle
numbers.each do |n|
    break if n == 5
end
```

### throw と catch を使って一気に脱出する

一気に外側のループまで脱出したい時は throw メソッドと catch メソッドを使う。

```ruby
catch タグ do
  throw タグ
end
```

```ruby
fruits = ['apple', 'melon', 'orange', 'banana']
numbers = [1, 2, 3, 4, 5]

catch :done do
    fruits.shuffle.each do |fruit|
        numbers.shuffle.each do |n|
            puts "#{fruit}, #{n}"
            if fruit == 'orange' && n == 3
                # すべての繰り返し処理を脱出する
                throw :done
            end
        end

    end
end
```

### next

繰り返し処理を途中で中断し、次の繰り返し処理に進める場合は next を使う。

```ruby
numbers.each do |n|
    next if n.even?
    puts n
end
```

## 配列

```ruby
a = [1, 2, 3]

# 配列の最後に要素を追加する
a << 2
a << 3

```

### 配列に初期値を設定する

```ruby
a = Array.new  # => []
a = Array.new(5, 1) # => [1, 1, 1, 1, 1]
a = Array.new(10) { |n| n % 3 + 1 }  # => [1, 2, 3, 1, 2, 3, 1, 2, 3, 1]
```


### 配列の繰り返し処理

- `|n|`の部分はブロック引数と呼ばれるもの
- `each`メソッドから渡された配列の要素が入る

```ruby
numbers = [1, 2, 3, 4]
sum = 0
numbers.each do |n|
    sum += n
end

puts sum  #10
```

`delete_if`は条件にマッチした要素を削除できる。

```ruby
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

a.delete_if do |n|
    n.odd?
end

puts a
```

- map メソッドを使うと、ブロックの戻り値が配列の要素となる新しい配列が作成される

```ruby
a = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

new_numbers = []
new_numbers = a.map { |n| n * 10 }
p new_numbers # [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]
```

- select メソッドは各要素に対してブロックを評価し、その戻り地が真の要素を集めた配列を返すメソッド

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8]

even_numbers = numbers.select { |n| n.even? }
p even_numbers # [2, 4, 6, 8]
```

- reject メソッドはブロックの戻り値が真になった要素を除外した配列を返す

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
non_multiples_of_three = numbers.reject { |n| n % 3 == 0 }
p non_multiples_of_three  # [1, 2, 4, 5, 7, 8]
```

- find メソッドはブロックの戻り値が真になった最初の要素を返す

```ruby
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
even_number = numbers.find { |n| n.even? }
p even_number  # 2
```

- inject メソッドはたたみ込み演算を行う
- 初回は`inject(n)`の`n`が`result`に入る
- ブロックの第 2 引数には配列の各要素が入る
- ブロックの戻り値は次の回に引き継がれ、ブロックの第 1 引数（result）に入る

```ruby
numbers = [1, 2, 3, 4, 5, 6]
sum = numbers.inject(0) { |result, n| result + n }

# (0 + 1 = 1) → (1 + 2 = 3) → (3 + 3 = 6) → (6 + 4 = 10) → (10 + 5 = 15) → (15 + 6 = 21)
p sum  # 21
```

### 要素の取得

- 2 つめから 3 つ分の要素を取り出す
  - `a[1, 3]`
- 最後の要素を取得する
  - `a[a.size - 1]`
  - `a.last`
- 最後から 2 番目の要素から 2 つの要素を取得する
  - `a[-2, 2]`

```ruby
a = [1, 2, 3, 4, 5]
p a[1, 3]  # [2, 3, 4]
p a[a.size - 1] # 5
p a[-1] # 5
p a.last # 5
p a.first # 1
p a[-2, 2] # [4, 5]
```

### 要素の追加

```ruby
a = []
a.push(1) # => [1]
a.push(2, 3) # => [1, 2, 3]


a = []
a << 1  # => [1]
a << 2  # => [1, 2]
a << 3  # => [1, 2, 3]
a # => [1, 2, 3]
```

### 要素の削除

```ruby
a = [1, 2, 3]
a.delete_at(1) # => 2
a # => [1, 3]
```

### 配列の連結

- `concat`は元の配列が変更される破壊的なメソッド
- `+`で連結した場合は元の配列は変更されない

```ruby
a = [1]
b = [2, 3]
a.concat(b) # => [1, 2, 3]
a #=> [1, 2, 3]

a = [1]
b = [2, 3]
a + b #=> [1, 2, 3]
a # [1]
```

### 文字列を配列に変換する

```ruby
'Ruby'.chars # => ["R", "u", "b", "y"]
'Ruby,Java,Perl,PHO'.split(',') # => ["Ruby", "Java", "Perl", "PHO"]
```

## Range

- `..`は最後の値を範囲に含める
- `...`は最後の値が範囲に含まれない

```ruby
range = 1..5  # 1以上5以下
range.include?(0) # => false
range.include?(1) # => true
range.include?(5) # => true
range = 1...5  # 1以上5未満
range.include?(0) # => false
range.include?(5) # => false
```

## ハッシュ

## シンボル

## 文字列

- 文字列中に改行文字（`\n`）を埋め込みたい場合は、ダブルクオートで囲む必要がある
- シングルクオートを使う場合は式展開されない

### %記法で文字列を作る

### ヒアドキュメント（行指向文字列リテラル）

```ruby
a = <<TEXT
ヒアドキュメントは
複数行に渡る長い文字列を作成するのに便利です。
TEXT
```

- ヒアドキュメントの中では式展開が有効

```ruby
name = 'fjsh'
a = <<TEXT
こんにちは！#{name}さん。
ヒアドキュメントの式展開です。
TEXT

puts a
```

### フォーマットを指定して文字列を作成

- `sprintf`メソッドを使うと、指定されたフォーマットの文字列を作成することができる

```ruby
sprintf('%0.3f', 1.2)  # 1.200
```

## その他

### 式（Expression）と文（Statement）

- 値を返し、結果を変数に代入できるものが式
- 値を返さず、変数に代入しようとすると構文エラーになるものが文

文ではなく式になっている if 文の例：

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

- 組み込みライブラリでない標準ライブラリや gem を利用する場合は、明示的にライブラリを読み込む必要がある。

```ruby
require 'date'
puts(Date.today) # 2020-12-16
```

自分で作成した Ruby プログラムを読み込む場合も`require`を使う。

- 拡張子を含む場合：`require './sample.rb'`
- 拡張子を含まなくても OK：`require './sample'`

### load

- `require`は 1 回しかライブラリや rb ファイルを読み込まない
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

## Visual Studio にインストールした Ruby 開発用プラグイン

- [VSCode の拡張機能で Rails と仲良くなる](https://qiita.com/hakshu/items/98ed12c32da97474b68d)
