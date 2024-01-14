---
title: "viteで作成したreactプロジェクトにimport aliasを設定する"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["vite", "react", "alias"]
published: true
---
## 学習元
Shin CodeさんのYouTube動画で詳しいことは解説されています。

@[card](https://www.youtube.com/watch?v=2E6iPVsGfPI)



## import alias
作成した`tsconfig.json`に以下を追加する

```json
"compilerOptions": {
  ...,
  /* import alias */
  "baseUrl": "./",
  "paths": {
    "@/*": ["src/*"],
  },
},
```



## 設定を反映
`tsconfig.json`を変更すると`vite.config.ts`も変更する必要があります。
しかし毎回どちらも変更するのは面倒なので、`tsconfig.json`の変更を自動で反映させるようにします。
以下のモジュールを追加

```bash
npm i -D vite-tsconfig-paths
```

`vite.config.ts`を変更

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react-swc'
import tsconfigPaths from "vite-tsconfig-paths"

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), tsconfigPaths()],
})

```


これでimport aliasの設定は完了です。