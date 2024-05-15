---
title: "Cloudflare D1 × Hono × Drizzle ORM"
emoji: "😽"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["hono", "drizzle"]
published: false
---

## プロジェクト作成

まずは Hono のセットアップを行います。
今回は`bun`を使用しますが、他に`npm`,`yarn`,`deno`でも可能です。

```bash
bunx create-hono d1-hono-drizzle
```

次にテンプレートを求められますので、`cloudflare-workers`を選択します。

```bash
✔ Using target directory … d1-hono-drizzle
? Which template do you want to use?
  aws-lambda
  bun
  cloudflare-pages
> cloudflare-workers
  deno
  fastly
  lambda-edge
(Use arrow keys to reveal more choices)
```

最後に依存関係のインストールをします。

```bash
bun i
```

## D1 作成

以下のコマンドで`d1`を作成します。
コマンドを実行する前に、ブラウザで Cloudflare にログインしておくと簡単に作成できます。

```bash
bunx wrangler d1 create d1-tutorial
```

上記コマンドを実行すると Cloudflare のサイトが開きますので、`Allow`を選択します。
その後、ターミナルに以下の表示が出力されれば成功です。
「You have granted authorization to Wrangler!」と表示されているブラウザは閉じて大丈夫です。
`database_id`は大事な値ですので、どこかに公開しないように注意してください。

```bash
✅ Successfully created DB 'd1-tutorial' in region APAC
Created your new D1 database.

[[d1_databases]]
binding = "DB" # i.e. available in your Worker on env.DB
database_name = "d1-tutorial"
database_id = "4be0fb76-c927-4d34-a461-4e1f98796124"
```

ターミナルに出力されている`[[d1_database]]`以下の情報を`wrangler.toml`に貼り付けします。

```:wrangler.toml
name = "d1-hono-drizzle"
compatibility_date = "2023-12-01"

# [vars]
# MY_VAR = "my-variable"

# [[kv_namespaces]]
# binding = "MY_KV_NAMESPACE"
# id = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# [[r2_buckets]]
# binding = "MY_BUCKET"
# bucket_name = "my-bucket"

[[d1_databases]]
binding = "DB" # i.e. available in your Worker on env.DB
database_name = "d1-tutorial"
database_id = "4be0fb76-c927-4d34-a461-4e1f98796124"

# [ai]
# binding = "AI"
```

以下のコマンドを実行し、

```bash
bun run dev
```

ターミナルに bindings に d1 の id が出力されていれば成功です。

```
Your worker has access to the following bindings:
- D1 Databases:
  - DB: d1-tutorial (4be0fb76-c927-4d34-a461-4e1f98796124)
```

## Drizzle 導入

以下のコマンドでインストールします。
`drizzle-kit`は sql ファイルを生成するのに使います。

```bash
bun add drizzle-orm
bun add -D drizzle-kit
```

### スキーマ定義作成

今回は`schema.ts`という名前でファイルを作成しました。

```ts:schema.ts
import { integer, sqliteTable, text } from "drizzle-orm/sqlite-core";

export const users = sqliteTable("users", {
  id: integer("id", { mode: "number" }).primaryKey({ autoIncrement: true }),
  name: text("name").notNull(),
  email: text("email").notNull(),
  password: text("password").notNull(),
});
```

### drizzle config 作成

`drizzle.config.ts`を作成し以下のように記入します。

```ts:drizzle.config.ts
import type { Config } from "drizzle-kit";

export default {
  schema: "./schema.ts",
  out: "./drizzle",
  driver: "d1",
  dialect: "sqlite",
  dbCredentials: {
    wranglerConfigPath: "wrangler.toml",
    dbName: "d1-tutorial",
  },
} satisfies Config;
```

- `schema` - スキーマ定義をしたファイルを指定します。
- `out` - スキーマ定義から生成した sql ファイル等が出力されます。
- `driver` - 使用するドライバーを指定します。接続 URL が使用できない場合に便利です。
- `dialect` - `postgresql`,`mysql`,`sqlite`から選択します。D1 では SQLite です。
- `wranglerConfigPath` - path という割に`./`が必要ないです。
- `dbName` - D1 作成時に指定したデータベース名を記入します。

### SQL ファイル生成

まず`package.json`にスクリプトを追記します。

```json:package.json
"generate": "drizzle-kit generate",
"local:migration": "wrangler d1 migrations apply d1-tutorial --local",
"remote:migration": "wrangler d1 migrations apply d1-tutorial --remote"
```

migration スクリプトの`apply`の次の単語は自身で指定したデータベース名になります。

では以下のコマンドで SQL ファイルを生成します。

```bash
bun run generate
```

以下のような文言が出力されれば成功です。

```bash
1 tables
users 4 columns 0 indexes 0 fks

[✓] Your SQL migration file ➜ drizzle\0000_wooden_norman_osborn.sql 🚀
```

### マイグレーション

次にローカルにマイグレーションをします。
まず`wrangler.toml`に以下を追記します。

```diff:wrangler.toml
[[d1_databases]]
binding = "DB" # i.e. available in your Worker on env.DB
database_name = "d1-tutorial"
database_id = "4be0fb76-c927-4d34-a461-4e1f98796124"
+ migrations_dir = "drizzle"
```

ディレクトリ先は`drizzle.config.ts`の out で指定したディレクトリを記入してください。
その次に以下のコマンドでマイグレーションを行います。

```bash
bun run local:migration
```

リクエストに対応できなくなる可能性がありますが続けますか？と聞かれるので、`y`を押します。

```bash
Migrations to be applied:
┌───────────────────────────────┐
│ name                          │
├───────────────────────────────┤
│ 0000_wooden_norman_osborn.sql │
└───────────────────────────────┘
? About to apply 1 migration(s)
Your database may not be available to serve requests during the migration, continue? » (Y/n)
```

以下のように出力されれば成功です。

```bash
🌀 Mapping SQL input into an array of statements
🌀 Executing on local database d1-tutorial (4be0fb76-c927-4d34-a461-4e1f98796124) from .wrangler\state\v3\d1:
🌀 To execute on your remote database, add a --remote flag to your wrangler command.
┌───────────────────────────────┬────────┐
│ name                          │ status │
├───────────────────────────────┼────────┤
│ 0000_wooden_norman_osborn.sql │ ✅       │
└───────────────────────────────┴────────┘
```

## CRUD 処理

Hono から D1 にアクセスし、CRUD を操作をするために以下のように修正します。

```ts:index.ts
import { drizzle } from "drizzle-orm/d1";
import { Hono } from "hono";
import { users } from "../schema";

type Bindings = {
  DB: D1Database;
};

const app = new Hono<{ Bindings: Bindings }>();

app.get("/", (c) => {
  return c.text("Hello Hono!");
});

/*****************************************
 * get users
 *****************************************/
app.get("/users", async (c) => {
  const db = drizzle(c.env.DB);
  const result = await db.select().from(users).all();
  return c.json(result);
});

/*****************************************
 * create users
 *****************************************/
app.post("/users", async (c) => {
  const params = await c.req.json<typeof users.$inferSelect>();
  const db = drizzle(c.env.DB);

  const result = await db.insert(users).values({
    name: params.name,
    email: params.email,
    password: params.password,
  });

  return c.json(result);
});

export default app;
```

以下の画像は Postman でユーザーの追加と取得をした結果になります。
ステータスコード 200 が返ってきていれば成功です。

![post users](/images/articles/20240515-cf-d1-hono-drizzle/post-users.png)

![get users](/images/articles/20240515-cf-d1-hono-drizzle/get-users.png)

## デプロイ

### マイグレーション

まず D1 を本番環境にマイグレーションします。

```bash
bun run remote:migration
```

ここでも続けていいですか？と聞かれるので、yes と答えて以下のように出力されれば成功です。

```bash
🌀 Mapping SQL input into an array of statements
🌀 Parsing 2 statements
🌀 Executing on remote database d1-tutorial (4be0fb76-c927-4d34-a461-4e1f98796124):
🌀 To execute on your local development database, remove the --remote flag from your wrangler command.
🚣 Executed 2 commands in 0.2992ms
┌───────────────────────────────┬────────┐
│ name                          │ status │
├───────────────────────────────┼────────┤
│ 0000_wooden_norman_osborn.sql │ ✅       │
└───────────────────────────────┴────────┘
```

### Hono

次に Hono を Cloudflare Workers にデプロイします。

```bash
bun run deploy
```

こちらも以下のように出力されれば成功です。

```bash
Your worker has access to the following bindings:
- D1 Databases:
  - DB: d1-tutorial (4be0fb76-c927-4d34-a461-4e1f98796124)
Total Upload: 75.18 KiB / gzip: 23.06 KiB
Uploaded d1-hono-drizzle (1.72 sec)
Published d1-hono-drizzle (3.90 sec)
  https://d1-hono-drizzle.daichi2mori.workers.dev
Current Deployment ID: 112b727c-e61b-4389-b2d4-fb44fbb4c0e8
Current Version ID: 112b727c-e61b-4389-b2d4-fb44fbb4c0e8
```

### CRUD 確認

本番環境に対しても以下のように成功しました。

![post users](/images/articles/20240515-cf-d1-hono-drizzle/prod-create-users.png)

![get users](/images/articles/20240515-cf-d1-hono-drizzle/prod-get-users.png)

## 参考

@[card](https://qiita.com/kmkkiii/items/2b22fa53a90bf98158c0)
@[card](https://zenn.dev/da1/articles/cloudflare-nextjs-hono-drizzle)
@[card](https://scrapbox.io/razokulover-tech-memo/Remix_+_Cloudflare_Pages_+_D1_+_KV_+_Queue_+_R2%E3%81%A7%E7%B0%A1%E5%8D%98%E3%81%AA%E3%82%A2%E3%83%97%E3%83%AA%E3%82%92%E4%BD%9C%E3%82%8B)
