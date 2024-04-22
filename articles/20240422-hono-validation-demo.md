---
title: "Honoでvalidationを試してみた"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["hono", "zod", "valibot"]
published: true
---

## 環境

- hono: 4.2.5
- zod: 3.23.0
- valibot: 0.30.0
- @hono/zod-validator: 0.2.1
- @hono/valibot-validator: 0.2.3

## Zod

```ts
import { Hono } from "hono";
import { z } from "zod";
import { zValidator } from "@hono/zod-validator";

const schema = z.object({
  str: z.string(),
  num: z.number(),
});

app.post(
  "/json",
  zValidator("json", schema, (result, c) => {
    if (!result.success) {
      return c.json({ message: "|||||／(￣ロ￣;)＼||||||| まじ～～？" }, 400);
    }
  }),
  (c) => {
    const { str, num } = c.req.valid("json");
    console.log(str, num);
    return c.text("ok");
  }
);
```

zod を使う場合には hono から `zod-validator` が提供されており、組み合わせることで簡単に検証ができる。
`zValidator`の第一引数には検証対象を指定し、`json`のほかに`form`,`query`,`header`,`param`,`cookie`がある。
第二引数にはスキーマを設定する。
第三引数は設定しなくても問題ないが、より細かくエラーハンドリングをしたいときに使用する。

`c.req.valid()`で検証後の値を取得でき、型補完もしてくれている。

## Valibot

hono では zod だけでなく valibot もサポートされており、使用方法は zod とほとんど変わらない。

```ts
import { Hono } from "hono";
import { number, object, string } from "valibot";
import { vValidator } from "@hono/valibot-validator";

const schema = object({
  str: string(),
  num: number(),
});

app.post(
  "/json",
  vValidator("json", schema, (result, c) => {
    if (!result.success) {
      return c.json({ message: "|||||／(￣ロ￣;)＼||||||| まじ～～？" }, 400);
    }
  }),
  (c) => {
    const { str, num } = c.req.valid("json");
    console.log(str, num);
    return c.text("ok");
  }
);
```

## 参考

https://takagi.blog/validation-for-hono-v3/
https://hono.dev/guides/validation
