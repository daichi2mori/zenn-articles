---
title: "bunでreact環境を作ろうとしたらダウンロードできずにエラーになった"
emoji: "🦁"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["npm"]
published: true
---

以下のコマンドを実行するとエラーになった

```bash
bunx create-vite
```

```bash
error: FailedToOpenSocket downloading package manifest create-vite
error: create-vite@latest failed to resolve
```

bun以外のpnpmで作ろうとしても上記とは違うエラーになりダメだった

とりあえず再起動したら作れるようになった

Windows側の問題？