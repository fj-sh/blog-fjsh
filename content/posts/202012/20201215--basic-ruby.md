---
title: "Rubyの基礎チートシート"
date: "2020-12-15T22:00:00.001Z"
template: "post"
draft: false
slug: "basic-ruby"
category: "Ruby"
tags:
  - "Ruby"
description: "Morbi in sem quis dui placerat ornare. Pellentesque odio nisi, euismod in, pharetra a, ultricies in, diam. Sed arcu. Cras consequat."
---

Rubyの文法の一覧を作成します。
ど忘れしたときの調べ物に使うためです。

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