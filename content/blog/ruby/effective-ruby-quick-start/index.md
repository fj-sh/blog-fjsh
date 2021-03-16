---
title: "Effective Rubyのまとめ"
date: "2020-03-09T01:01:01.284Z"
category: "dev"
description: "Effective Rubyを学んだ内容をまとめる"
emoji: "😎"
---

## Rubyは何を真と考えているか

- falseとnilを除くすべての値が真
- ゼロは真
- true, falseはグローバル変数と考える
- falseとnilを区別しなければならないときは`nil?`メソッドを使うか、falseを左被演算子とする`==`演算子を使う


## オブジェクトを扱うときにはnilである可能性に注意する

- ダックタイピングとは、オブジェクトが特定のクラスのインスタンスであることを要求せず、オブジェクトに何ができるのかに着目点をずらす（型よりもインターフェース）
- オブジェクトのメソッドを呼び出し、オブジェクトがそのメソッドに対して反応しなければ、実行時にRubyが`NoMethodError`例外を生成するのに任せる
- すべての変数がnilかもしれないという前提でコードを書く

## 定数がミュータブルであることに注意する

- 定数は大文字で始まる
- `freeze`メソッドを使って定数をイミュータブルなものにする
- 定数で配列やハッシュなどのコレクションオブジェクトを算用するときは、コレクションと要素をフリーズする
- 既存の定数に新しい値が代入されるのを防ぐためには、定数が定義されているモジュールをフリーズする

```ruby
module Defaults
  NETWORKS = [
    "192.168.1",
    "192.168.2",
  ].map!(&:freeze).freeze
end

>> Defaults::NETWORKS << "localhost"
Traceback (most recent call last):
        4: from /Users/fj/.rbenv/versions/2.6.4/bin/irb:23:in `<main>'
        3: from /Users/fj/.rbenv/versions/2.6.4/bin/irb:23:in `load'
        2: from /Users/fj/.rbenv/versions/2.6.4/lib/ruby/gems/2.6.0/gems/irb-1.0.0/exe/irb:11:in `<top (required)>'
        1: from (irb):14
FrozenError (can't modify frozen Array)

# モジュールをフリーズするパターン

module Defaults2
  TIMEOUT = 5
end

>> Defaults2.freeze
>> Defaults2::TIMEOUT
=> 5
>> Defaults2::TIMEOUT = 10
Traceback (most recent call last):
        4: from /Users/fj/.rbenv/versions/2.6.4/bin/irb:23:in `<main>'
        3: from /Users/fj/.rbenv/versions/2.6.4/bin/irb:23:in `load'
        2: from /Users/fj/.rbenv/versions/2.6.4/lib/ruby/gems/2.6.0/gems/irb-1.0.0/exe/irb:11:in `<top (required)>'
        1: from (irb):22
FrozenError (can't modify frozen Module)
```