---
title: "react環境変数"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "vite"]
published: false
---
## 環境変数の設定
reactで環境変数を設定するには`.env`ファイルを作成する必要がある。
create-react-appで作成した場合と、Viteで作成した場合で変数名が少し変わる。


### create-react-appの場合
変数名の先頭に`REACT_APP_`を付ける

```env
REACT_APP_XXXXX=123456789
```

環境変数を使うときは`process.env.`を使う

```js
const key = process.env.REACT_APP_XXXXX;
```


### Viteの場合
こちらは先頭に`VITE_`が必要

```env
VITE_XXXXX=987654321
```

使うときは`import.meta.env.`になる

```js
const key = import.meta.env.VITE_XXXXX;
```

環境変数を変更した場合はサーバーを再起動しないとエラーになる



## 参考サイト
@[card](https://coders-shelf.com/create-react-app-env-variables/)
@[card](https://ja.vitejs.dev/guide/env-and-mode.html)