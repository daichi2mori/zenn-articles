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
```bash
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```


## プレビューする
```bash
npx zenn preview
```

デフォルトではlocalhost:8000で立ち上がりますが、
npx zenn preview --port 3000というようにポート番号の指定もできます。


## 日時を指定して記事を公開する（公開予約する）
公開時間を指定して記事を公開するには、Front Matterにて published を true にした上で、 published_at を指定します。
published_at のフォーマットは、YYYY-MM-DD または YYYY-MM-DD hh:mm です。日付だけを指定した場合、時刻は 00:00 となります。

```bash
published: true # trueを指定する
published_at: 2050-06-12 09:00 # 未来の日時を指定する
```