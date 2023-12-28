---
title: "husky + lint-staged + prettierの設定"
emoji: "🐡"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["husky", "lint-staged", "prettier"]
published: false
---

## Gitの初期化
```bash
git status
> fatal: not a git repository (or any of the parent directories): .git
```
fatalと出てきたらリポジトリはないのでGitを初期化する

```bash
git init
```

```bash
bun add -D husky lint-staged prettier
```

```diff json
{
   ...,
+  "lint-staged": {
+    "*.{ts,tsx}": [
+      "prettier --write"
+    ]
+  }
}
```

```bash
bunx husky install
> husky - Git hooks installed
```

```bash
bunx husky add .husky/pre-commit "bunx lint-staged"
> husky - created .husky/pre-commit
```

```diff json
   "scripts": {
     ...,
+    "lint-staged": "lint-staged"
   }
```

package.json
```diff json
    {
   ...,
+  "prettier": {
+    "semi": false,
+    "singleQuote": true,
+    "tabWidth": 2,
+    "trailingComma": "all"
+  }
}
```

- semi : 末尾にセミコロンをつけるかどうか
- singleQuote : シングルクォートに変換するかどうか
- tabWidth : インデントのスペースの数
- trailingComma : 複数行の場合に末尾にカンマをつけるか

tsconfig.json
```diff json
    {
   "compilerOptions": {
      ...,
+    "baseUrl": "src"
      ...,
     "strict": true,
+    "noImplicitReturns": true,
     "noUnusedLocals": true,
     "noUnusedParameters": true,
     "noFallthroughCasesInSwitch": true,
   }
}
```

- noImplicitReturns : 数内のすべての経路で、返り値の型があっているかをチェックする。
- noUnusedLocals : 宣言して、一度も使われないローカル変数があると、コンパイルエラーになる。
- noUnusedParameters : 一度も使われないパラメータがあると、コンパイルエラーになる。