---
title: "AtCoder初中級者が解くべき過去問精選100問をPythonで解く（１）"
date: "2020-12-17T22:00:00.001Z"
template: "post"
draft: false
slug: "atcoder-100-python"
category: "アルゴリズムとデータ構造"
tags:
  - "Python"
description: "AtCoderで"
---


[分野別　初中級者が解くべき過去問精選 100 問](https://qiita.com/e869120/items/eb50fdaece12be418faa#2-3-%E5%88%86%E9%87%8E%E5%88%A5%E5%88%9D%E4%B8%AD%E7%B4%9A%E8%80%85%E3%81%8C%E8%A7%A3%E3%81%8F%E3%81%B9%E3%81%8D%E9%81%8E%E5%8E%BB%E5%95%8F%E7%B2%BE%E9%81%B8-100-%E5%95%8F)を参考に、AtCoderの過去問を100問解いていく記事です。



## [B - 1](https://atcoder.jp/contests/abc106/tasks/abc106_b)

> 問題文
105という数は, 非常に特殊な性質を持つ. 奇数なのに, 約数が 
8個もある. さて, 1以上N以下の奇数のうち, 正の約数を ちょうど8個持つようなものの個数を求めよ.


約数の問題です。
Nで与えられた数以下の「奇数」のうち、正の約数を8個持つ数字の個数を求めます。

以下のアルゴリズムで解きます。

- 与えられたNまでの数字を一つ一つ精査する
- 一つ一つ精査した数字の中に約数がいくつあるか精査する
- 約数が8個だった場合は、答えの数字に1足す

```python
n = int(input())

ans = 0

for i in range(1, n+1):
    count = 0
    for j in range(1, i+1):

        # 奇数であることを確認する
        if i % 2 != 0:
            # 約数であることを確認する
            if i % j == 0:
                count += 1
    # 約数が8個であったらans+1
    if count == 8:
        ans += 1

print(ans)

```

約数について復習しておきましょう。

- 「約数」…ある整数をわり切ることができる整数をある数の約数といいます。
- 「公約数」…2つ以上の整数の共通な約数を、それらの整数の公約数といいます。
- 「最大公約数」…公約数の中で、最も大きい約数を最大公約数といいます。

[約数・公約数・最大公約数（第1回）](https://www.manabinoba.com/math/2718.html#01)

Pythonの`range`関数についても復習しておきます。
`range(start, stop, step)`のように引数に整数を3つ指定すると`start ≦ i < stop`でstepずつ増加する等差数列が生成されます。

今回の問題では「奇数」に対象が絞られていたので、step部分を「2」とすると、偶数値のループを飛ばすことができます。

[Pythonのrange関数の使い方](https://note.nkmk.me/python-range-usage/)