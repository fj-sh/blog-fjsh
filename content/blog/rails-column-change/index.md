---
title: "Railsでカラムを変更した"
date: "2020-02-21T01:01:01.284Z"
category: "dev"
description: "マイグレーションスクリプトを編集してカラムを変更した"
emoji: "🐀"
---

『Ruby on Rails6 実践ガイド』 Chapter16を勉強しているときに、viewがうまく表示されない問題が発生した。

原因を調べてみたところ、 `bin/rails g model address`でaddressテーブルを作成する際に、`default: ""`の指定を忘れていたことに気付いた。

## 間違っていた内容

```ruby:title="20210221052742_create_addresses.rb"
class CreateAddresses < ActiveRecord::Migration[6.0]
  def change
    create_table :addresses do |t|
      t.references :customer, null: false
      t.string :type, null: false
      t.string :postal_code, null: false
      t.string :prefecture, null: false
      t.string :city, null: false
      t.string :address1, null: false
      t.string :address2, null: false
      t.string :company_name, null: false
      t.string :division_name, null: false, default: ""

      t.timestamps
    end

    add_index :addresses, [ :type, :customer_id ], unique: true
    add_foreign_key :addresses, :customers
  end
end

```

## 変更するために作成したもの

`bin/rails g migration Addresses`でマイグレーションスクリプトを生成。

マイグレーションスクリプトに以下のように記載した。

```ruby:title="20210221072031_addresses.rb"
class Addresses < ActiveRecord::Migration[6.0]
  # https://railsguides.jp/active_record_migrations.html
  def change
    change_column_default :addresses, :company_name, from: nil, to: ""
  end
end
```

その後、`bin/rails db:migrate`を実行したら、無事に通った。

シードデータを投入するために`bin/rails db:reset`を実行した。

## 参考

[Active Record マイグレーション 3.4 カラムを変更する](https://railsguides.jp/active_record_migrations.html#%E3%82%AB%E3%83%A9%E3%83%A0%E3%82%92%E5%A4%89%E6%9B%B4%E3%81%99%E3%82%8B)

Railsで迷ったときはRailsガイドが一番良いと思った。
その他のサイトは情報が古かったり、必要な内容が記載されていなかった。

「まずRailsガイド」で検索するようにしたい。