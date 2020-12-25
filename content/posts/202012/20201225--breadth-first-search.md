---
title: "幅優先探索"
date: "2020-12-25T01:00:00.001Z"
template: "post"
draft: false
slug: "breadth-first-search"
category: "アルゴリズムとデータ構造"
tags:
  - "アルゴリズムとデータ構造"
description: "幅優先探索について解説する記事です。"
---

幅優先探索（BFS:Breadth-first search）は最短経路問題を解くためのアルゴリズムです。
幅優先探索を使うと、2 点間の最短距離を見つけ出すことができます。

たとえば東京から大阪に移動する方法を突き止めるには、以下の 2 つのステップが必要です。

1. 問題をグラフとしてモデル化する
2. 幅優先探索を使って問題を解く

## グラフとは何か

グラフは一連のつながりをモデル化するものです。
グラフはそれぞれノード（note）と辺（edge）で構成されます。

「田中さんが鈴木さんにお金を借りている」みたいな、二者間のつながりは以下のようにグラフにできます。

![ノードと辺](/media/202012/node_and_edge.png)

グラフはさまざまなものが相互に結びつく様子をモデル化する手段です。

## Googler を探せ

あなたがどうしても Google に転職したいとします。
転職するにはリファラル採用がいいと聞きました。
リファラル採用されるためには、Googler（Google 社員）と知り合いにならなければいけません。

なんとか、知り合いのツテを辿って Google 社員を見つけたいとします。
そんな Google 社員探しに役に立つのが幅優先探索です。

![Googlerを探せ](/media/202012/search_googler.png)

幅優先探索を行うには、人間関係を「集合」で表します。

```python
graph = {}
graph["you"] = ["goku", "hanako", "piko"]
graph["piko"] = ["gohan"]
graph["hanako"] = []
graph["goku"] = ["lufi", "beji", "google"]
graph["lufi"] = []
graph["beji"] = []
graph["google"] = []


def person_is_googler(name):
    return name == "google"

from collections import deque

def search(name):
    search_queue = deque()
    search_queue += graph[name]
    searched = []
    while search_queue:
        person = search_queue.popleft()
        if not person in searched:
            if person_is_googler(person):
                print(person + " is a googler")
                return True
            else:
                search_queue += graph[person]
                searched.append(person)
    return False

search("you")
```

結果
↓
```
google is a googler
```
