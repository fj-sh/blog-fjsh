---
title: "Pythonでモジュールを利用する"
date: "2020-12-23T22:00:00.001Z"
template: "post"
draft: false
slug: "python-module"
category: "Python"
tags:
  - "Python"
description: "Pythonのモジュールの使い方"
---

Pythonのクラスや関数の定義が書かれたファイルはモジュールと呼ばれます。
モジュールはhogehoge.pyという`.py`付きのファイルの形で作成されます。

「.py」までの拡張子の前までがモジュール名となります。

特別な理由がない限りは、Pythonのモジュール名はアルファベットの小文字だけを使います。

## モジュールをimportする

`import モジュール名`でモジュールを読み込むことができます。

```python
import random

print(random.random())
print(random.randint(0, 6))

a_list = [0, 1, 2, 3, 4, 5]
random.shuffle(a_list)
print(a_list)
```

`as`を使うと読み込んだモジュールのモジュール名を一時的に変更できます。

`import matplotlib.pyplot as plt`の中の`as plt`という部分は「pltとして読み込む」という意味です。

### fromを使ったインポート

`from モジュール名 import 関数名`でモジュールにある関数などを直接インポートできます。

```python
from statistics import median

monk_fish_team = [158, 157, 163, 157, 145]
volleyball_team = [143, 167, 170, 165]

print(median(monk_fish_team))  # 157
print(median(volleyball_team))  # 166.0
```

## モジュールの作成

拡張子.pyのファイルを作成します。

```python:encoder.py
import base64


def str_to_base64(x):
    return base64.b64encode(x.encode('utf-8'))

```

## モジュールのインポート

モジュールを作成したディレクトリで`import 拡張子を抜いたファイル名`を実行してみましょう。
モジュールがインポートできます。

```python
>>> import encoder
>>> type(encoder)
<type 'module'>
>>> dir(encoder)
['__builtins__', '__doc__', '__file__', '__name__', '__package__', 'base64', 'str_to_base64']
>>> encoder.str_to_base64('python')
'cHl0aG9u'
>>> encoder.__name__
'encoder'
```

## モジュール検索順

Pythonもモジュール検索は以下の順で行われます。

1. ビルトインモジュール
2. カレントディレクトリのモジュール
3. 環境変数`PYTHONPATH`に設定されているライブラリ
4. ビルトインモジュール以外の標準ライブラリ
5. 追加のモジュールを設置するためのディレクトリ

## サードパーティのモジュールを使う方法

PyPI(Python Package Index)はPythonのモジュールが集められたサービスです。
`pip`を使うと、PyPIに登録されているモジュールを手軽にインストールすることができます。
`pip`はライブラリの依存関係も解決してくれます。

`python -m pip install ライブラリ名`で目的のライブラリをインストールできます。

既にインストールしているライブラリのアップデートは`python -m pip install --upgrade ライブラリ名`でできます。