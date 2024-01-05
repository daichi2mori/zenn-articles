---
title: "「Netflixのクローンを作るチュートリアル」で躓いたこと"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "TypeScript", "Vite"]
published: true
---

こちらの本を学習中にエラーで躓いたことを書いていきます。

@[card](https://zenn.dev/gunners6518/books/4c4672f32dd100)



## Chapter3
### .envに設定したAPIキーがundefinedになる
私は環境を作るときにVITEを使っていたため、いろいろ記法が違っていました。

* `VITE_XXXX`といった表記である必要がある
* 呼び出すときは`import.meta.env.VITE_XXXX`と書く

```js
VITE_API_KEY=XXXXXXXXXXX

const API = import.meta.env.VITE_API_KEY;
```



## Chapter4
### APIからデータを取得できない
原因としてはChapter3で作成したaxios.jsを使っていなかったです。
Row.tsxで使っているaxiosは、axios.jsでexportしたinstanceを、axiosに名前を変更して使っていました。
私は勘違いしてmoduleのaxiosから直接importしていたため、URLの値が不正でうまくデータを取得できませんでした。

```js
import instance from "../axios";

export const Row = ({ title, fetchUrl }: Props) => {
  const [movies, setMovies] = useState<Movie[]>([]);

  useEffect(() => {
    async function fetchData() {
      const request = await instance.get(fetchUrl);
      setMovies(request.data.results);
      return request;
    }
    fetchData();
  }, [fetchUrl]);

  console.log(movies);

  return(
    <div className="Row" />
  );
};
```

上記のコードの`instance`がaxios.jsで作成した関数です。


### 「DOM部分の表示」でエラーになる
Rowのreturn部分をコピペしたところ`isLargeRow`と`base_url`が見つかりませんというエラーになります。
`isLargeRow`に関しては、App.tsxから渡されるpropsなので下記のように追加すると解決します。

```diff tsx
- export const Row = ({ title, fetchUrl }: Props) => {
+ export const Row = ({ title, fetchUrl, isLargeRow }: Props) => {
```

`base_url`は下記のようにURLを直接設定します。

```diff tsx
import instance from "../axios";
import { useEffect, useState } from "react";

+ const base_url = "https://image.tmdb.org/t/p/original";

type Props = {
  ・・・
};

type Movie = {
  ・・・
};

export const Row = ({ title, fetchUrl, isLargeRow }: Props) => {
  ・・・
};

```


### スタイリングの方法
チュートリアルではscssを用いてスタイリングしています。
scssファイルを使えるようにするにはパッケージをインストールする必要があります。

```bash
npm install sass
```

上記のコマンドだけでscssファイルが使用できます。
次にRow.tsx用にscssファイルを作成します。
作成場所はどこでもいいのですが、今回はわかりやすいようにRow.tsxと同階層に同じ名前で作成します。

```bash
.
└─ src
  ├─ components
    ├─ Row.tsx
    └─ Row.scss
```

scssファイルの作成が完了後は、Row.tsxにimportします。

```tsx
import "./Row.scss";
```