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

## Typebox

Typebox に関しても zod と使用方法は変わらない

```ts
import { Hono } from "hono";
import { Type } from "@sinclair/typebox";
import { tbValidator } from "@hono/typebox-validator";

const app = new Hono();

const schema = Type.Object({
  str: Type.String(),
  num: Type.Number(),
});

app.post(
  "/json",
  tbValidator("json", schema, (result, c) => {
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

## Typia

Typia は他と使用方法が異なっている。
スキーマを設定するには、コンパイルを行い Typia 用のファイルを生成する必要がある。
まず以下のように interface を設定し、createValidate メソッドのジェネリクスに入れる。

```ts
import typia from "typia";

interface IMember {
  str: string;
  num: number;
}

export const typiaValidate = typia.createValidate<IMember>();
```

その後以下の生成用コマンドを実行する。
私は src/templates に先ほど記述したファイルを格納しており、生成後は src/generated に出力されるようにしている。

```bash
npx typia generate --input src/templates --output src/generated --project tsconfig.json
```

生成されたファイルからスキーマを import すると、あとは zod 等と使用方法は同じ。

```ts
import { Hono } from "hono";
import { typiaValidator } from "@hono/typia-validator";
import { typiaValidate } from "./generated/typia";

const app = new Hono();

app.post(
  "/json",
  typiaValidator("json", typiaValidate, (result, c) => {
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

Typia には transform mode というのがあるけどよくわからなかった。。

## 参考

https://takagi.blog/validation-for-hono-v3/
https://hono.dev/guides/validation
https://typia.io/docs/setup/#transformation
https://typia.io/docs/validators/validate/
