# drizzle-pg-proxy-bun

Containerized [Postgres HTTP proxy](https://orm.drizzle.team/docs/get-started-postgresql#http-proxy) for [Drizzle ORM](https://orm.drizzle.team/docs/overview), running on [Bun](https://bun.sh/) with [Hono](https://hono.dev/).

## Setup

```bash
$ git clone git@github.com:teamreflex/drizzle-pg-proxy-bun.git
$ cd drizzle-pg-proxy-bun
$ bun install
$ cp .env.example .env
$ docker compose up -d
```

Your Drizzle client can now connect to the proxy:

```ts
import { drizzle } from "drizzle-orm/pg-proxy";
import * as schema from "./schema";

export const db = drizzle(
  async (sql, params, method) => {
    try {
      const rows = await fetch(`http://localhost:8080/query`, {
        method: "POST",
        headers: {
          "proxy-key": process.env.PROXY_KEY,
        },
        body: JSON.stringify({ sql, params, method }),
        cache: "no-cache",
      }).then((res) => res.json());

      return { rows };
    } catch (err) {
      console.error("Error from Drizzle HTTP proxy: ", err);
      return { rows: [] };
    }
  },
  { schema }
);
```

## License

Licensed under the [MIT license](https://github.com/teamreflex/drizzle-pg-proxy-bun/blob/main/LICENSE.md).
