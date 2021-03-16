---
title: "Railsのマイグレーション操作"
date: "2020-03-10T01:01:01.284Z"
category: "dev"
description: "マイグレーションまとめ"
emoji: "😁"
---

マイグレーションは以下の2ステップで行う。

- DBの構造を変更するコードをRubyで記述したマイグレーションファイルを作成する
- 作成したマイグレーションファイルを`db:migrate`コマンドを使ってデータベースに適用する


マイグレーションはデータベースごとに適用する必要がある。
デフォルトでは開発用データベースに対してマイグレーションが適用される。

## マイグレーションを実行する

```
rails db:migrate
```

本番データベースにマイグレーションを実行する。

```
rails db:migrate RAILS_ENV=production
```

テスト用データベースにマイグレーションを実施する。

```
rails db:migrate RAILS_ENV=test
```

## マイグレーションに関するコマンド

|コマンド|意味|
| --- | --- |
|bin/rails db:migrate|最新までマイグレーションを適用する|
|bin/rails db:migrate VERSION=2018060851058|特定のバージョンまでマイグレーションが適用された状態にする|
|bin/rails db:rollback|バージョンを1つ戻す|
|bin/rails db:rollback STEP=2|バージョンを指定したステップ数だけ戻す|


## マイグレーションファイルを作成する

```
 bin/rails g migration ChangeTaskNameNotNull
```

## NOT NULL制約を加える

あとからNOT NULL制約を追加する場合。

```
class ChangeTaskNameNotNull < ActiveRecord::Migration[5.2]
  def change
    change_column_null :tasks, :name, false
  end
end
```

最初からNOT NULL制約をつける場合。

```
class CreateTasks < ActiveRecord::Migration[5.2]
  def change
    create_table :tasks do |t|
      t.string :name, null: false
      t.text :description

      t.timestamps
    end
  end
end
```


## 文字列カラムの長さを指定する

あとからlimitを追加する場合。

```
class ChangeTaskNameLimit < ActiveRecord::Migration[5.2]
  def up
    change_column :tasks, :name, :string, limit: 30
  end

  def down
    change_column :tasks, :name, :string
  end
end

```

最初から文字列の長さを制限する場合。

```
class CreateTasks < ActiveRecord::Migration[5.2]
  def change
    create_table :tasks do |t|
      t.string :name, limit: 30, null: false
      t.text :description

      t.timestamps
    end
  end
end

```

