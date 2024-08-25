---
title: "bunとviteを使ってreact構築"
emoji: "👍"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["react", "bun", "vite"]
published: true
---

## 公式サイトはこちら

@[card](https://vitejs.dev/guide/)
@[card](https://bun.sh/guides/ecosystem/vite)

## プロジェクト作成

1. コマンド実行

```bash
bun create vite
```

2. プロジェクト名
   上記コマンドを実行するとプロジェクト名を聞かれるので、好きな名前を入力する

```bash
? Project name: › my-react-app
```

3. フレームワーク選択
   React を選択した状態で Enter

```bash
? Select a framework: › - Use arrow-keys. Return to submit.
    Vanilla
    Vue
❯   React
    Preact
    Lit
    Svelte
    Solid
    Qwik
    Others
```

4. 言語とトランスパイラー選択
   今回は TypeScript + SWC を選択します。

:::details SWC とは？
Rust で作成されたトランスパイラーで、Babel より高速に動きます。
最近では SWC がデフォルトスタンダードになっています。
:::

```bash
? Select a variant: › - Use arrow-keys. Return to submit.
    TypeScript
❯   TypeScript + SWC
    JavaScript
    JavaScript + SWC
```

5. 依存関係インストール
   `cd`でプロジェクトディレクトリに移動し、依存関係をインストールする

```bash
cd my-react-app
```

```bash
bun install
```

6. スクリプト編集
   開発用と本番用のスクリプトを変更する

```diff json
"scripts": {
-  "dev": "vite",
-  "build": "vite build",
+  "dev": "bunx --bun vite",
+  "build": "bunx --bun vite build",
},
```

7. 開発サーバーを起動する

```bash
bun run dev
```

以下のような画面が起動すれば成功
![react起動画面](/images/articles/20231217-bun-vite-react/image.webp =400x)

## オプションでテンプレートを直接指定

```bash
bunx create-vite my-react-app --template react-ts
cd my-react-app
bun install
bun run dev
```

上記のコマンドを実行することで、Vite が用意してくれているテンプレートを使用でき、後は依存関係をインストールするだけですぐに開発できるようになる
しかし、このテンプレートのトランスパイラーは Babel を使用している点に注意
