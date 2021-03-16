---
title: "Railsでモデルの検証を行う"
date: "2020-03-10T01:01:01.284Z"
category: "dev"
description: "Railsのモデルの検証のやり方"
emoji: "🐀"
---


Railsにおけるモデルの検証は基本的に、モデルオブジェクトをデータベースに登録・更新する前に検証を行い、エラーがあれば登録・更新をしないで差し戻す仕組みになっている。


## 必須かどうかの検証を追加

Taskモデルのname属性の値が入っていなければ検証エラーになるようにする。

```ruby:title=app/models/task.rb
class Task < ApplicationRecord
  validates :name, presence: true
end
```


## コールバック

コールバックとは、あとで呼んでほしい処理をあらかじめ指定しておく仕組みのこと。

Railsのモデル層のコールバックは、モデルオブジェクトの重要なイベントの前後に任意の処理をいくつでも呼び出せる仕組みになっている。

RaislではActiveRecordモデルに対して、イベントの前後にコールバック処理を置くことができる。

before、after、aroundのタイミングでコールバックを書くことができる。

```ruby:title=app/models/task.rb
class Task < ApplicationRecord
  before_validation :set_nameless_name

  validates :name, presence: true
  validates :name, length: { maximum: 30 }
  validate :validate_name_not_including_comma

  private

  def set_nameless_name
    self.name = '名前なし' if name.blank?
  end

  def validate_name_not_including_comma
    errors.add(:name, 'にカンマを含めることはできません') if name&.include?(',')
  end
end
```

## トランザクション

トランザクションは一連の複数の処理によるデータベースの整合性を保つための機能。

Railsではコールバック全体を1つのトランザクションで囲ってsaveを実行する。

そのためコールバックの1つで例外が発生すると、ロールバックが発生し、以降のコールバックは実行されない。
