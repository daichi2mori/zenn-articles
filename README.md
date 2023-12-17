# Zenn CLI

* [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)


## ファイルの配置ルール
1 つの記事の内容は、1 つのmarkdownファイル（◯◯.md）で管理します。
ファイルはarticlesという名前のディレクトリ内に含める必要があります。

```bash
.
└─ articles
   ├── example-article1.md
   └── example-article2.md
```


## 記事の作成
```bash
npx zenn new:article
```

コマンド実行時に記事の Front Matter をオプションで指定することもできます。
slug はa-z0-9、ハイフン-、アンダースコア_の 12〜50 字の組み合わせにする必要があります
```bash
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```

作成されたファイルの中身は次のようになっています。
```bash
---
title: "" # 記事のタイトル
emoji: "😸" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: [] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
published_at: 2050-06-12 09:00 # 未来の日時を指定すると公開予約になる
---
ここから本文を書く
```

公開時間を指定して記事を公開するには、published を true にした上で、 published_at を指定します。
published_at のフォーマットは、YYYY-MM-DD または YYYY-MM-DD hh:mm です。日付だけを指定した場合、時刻は 00:00 となります。



## プレビューする
```bash
npx zenn preview
```

デフォルトではlocalhost:8000で立ち上がりますが、
npx zenn preview --port 3000というようにポート番号の指定もできます。