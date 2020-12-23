---
title: "Pythonでパッケージを利用する"
date: "2020-12-23T02:00:00.001Z"
template: "post"
draft: false
slug: "python-package"
category: "Python"
tags:
  - "Python"
description: "Pythonのパッケージの使い方"
---

複数のモジュールをまとめる機能がパッケージです。
パッケージの実体は、モジュールやサブパッケージを納めたディレクトリです。
Pythonではパッケージの目印として、`__init__.py`を配置します。


## パッケージの作成

パッケージを作成するためにはまず、ディレクトリを作ります。
ディレクトリの中に`__init__.py`を作ればパッケージの完成です。

以下は`b64`パッケージを作成したときのディレクトリ構成です。
```
├── b64
│   ├── __init__.py
│   ├── decoder.py
│   └── encoder.py
```

## パッケージを利用する

パッケージは`from パッケージ名 import モジュール名`文で利用できます。

```python
>>> from b64 import encoder, decoder
>>> encoder.str_to_base64('python')
b'cHl0aG9u'
>>> decoder.base64_to_str(b'cHl0aG9u')
'python'
```

### パッケージの階層構造


パッケージをimportしたモジュールを利用するときは、モジュール名を省略することはできません。

たとえば、以下のようなディレクトリ構成でone/two/sample.pyの中のhello()関数を利用するには`import one.two.sample`した上で、`ont.two.sample.hello()`を呼ばなければいけません。

```
├── one
│   ├── __init__.py
│   └── two
│       ├── __init__.py
│       └── sample.py
```

sample.pyの中身
```python
def hello():
    print('パッケージの使い方')

```


```python
>>> import one.two.sample
>>> one.two.sample.hello()
パッケージの使い方
```

`from パッケージ名 import モジュール名`とすることで、間の階層を飛び越してモジュールを読み込み、定義することができます。

```python
>>> from one.two import sample
>>> sample.hello()
パッケージの使い方
```

## __init__.pyの機能

__init__.pyはパッケージを初期化する機能があります。__init__.pyで相対インポートを記述することで、パッケージ名.属性名で機能が呼び出せるようになります。



ディレクトリ構造は以下のとおりです。
```
├── b64
│   ├── __init__.py
│   ├── decoder.py
│   └── encoder.py
```


init.pyに以下のように`from import`文を書きます。
```python
from .encoder import str_to_base64
from .decoder import base64_to_str
```


```python
>>> import b64
>>> dir(b64)
['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'base64_to_str', 'decoder', 'encoder', 'str_to_base64']
>>> 
>>> b64.str_to_base64('python')
```


