---
title: "markdownテスト"
emoji: "🐈"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [markdown]
published: true
published_at: 2023-12-13 14:00
---

## よく使いそうなmarkdown記法を書いていく
**zennで使える記法は公式ページを参照**
@[card](https://zenn.dev/zenn/articles/markdown-guide)



## コードブロック
コードは`「```」`で挟んだ文字がコードとして表示される。
最初の`「```」`の後ろにjsなどの言語を指定すると、シンタックスハイライトが適用される。

:::details シンタックスハイライトとは
指定された言語によって特定のキーワードごとに異なる色で表示してくれる機能
:::

> ` ```js `
>
> ` ``` `

```js
const test = () => {
  console.log("テストです");
};
```

[zennが対応している言語一覧はこちら](https://prismjs.com/#supported-languages)



## 画像
```
![altテキスト](https://画像のURL)
```

![浜辺の洞窟](/images/3c19a9366f7b17/ocean.webp)

画像の幅はURLの後ろに半角スペースを開けて`=00x`という記述で、画像幅をpx単位で指定できる。

```
![altテキスト](https://画像のURL =250x)
```

![浜辺の洞窟](/images/3c19a9366f7b17/ocean.webp =250x)



## 引用
```
> 引用文
> 引用文
```
> 引用文
> 引用文



## テキストリンク
```
[ここの文字をクリックするとリンク先に飛ぶ](リンクのURL)
```
[ここの文字をクリックするとリンク先に飛ぶ](https://zenn.dev/zenn/articles/markdown-guide#%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E3%83%AA%E3%83%B3%E3%82%AF)


## Zenn独自の記法
### メッセージ
```
:::message
ここにメッセージを記入
:::
```
:::message
こんなかんじで表示される
:::

### アラート
```
:::message alert
警告メッセージも出せる
:::
```
:::message alert
警告メッセージも出せる
:::



## とりあえず
ひとまずよく使うのはこれくらいのはず、、、
使っていくうちにこれもよく使ってるなと思ったらまた追加する予定