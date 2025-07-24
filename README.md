# Reom

![Reom Logo](https://dummyimage.com/400x120/000/ffffff\&text=Reom)

**A lightweight, client‑agnostic Redis ORM for Node.js & Edge.**

> *Hash‑only. Zero dependencies in core. Pick just the packages you need.*

---

## ✨ Why Reom?

| Problem                                                        | Reom’s answer                                                                  |
| -------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| Redis SDK APIs differ (`ioredis`, `redis@4`, Upstash…).        | **Adapter Ports** keep core driver‑agnostic.                                   |
| Boilerplate for flattening nested objects into Hash fields.    | **Core** provides `flatten` / `inflate` utilities & compile‑time `DotKeys<T>`. |
| Needing just Hash CRUD but frameworks add JSON & search bloat. | **Hash‑only repository** ‑‑> tiny bundle (<6 kB).                              |
| Extra magic wanted (auto schema, indexes, live updates).       | Opt‑in **`@reom/magic-*`** plugins.                                            |

---

## 🏗️ Package Map

```
@reom/core                // types, flatten utils, schema registry
@reom/ports               // minimal RedisClientPort interface
@reom/repository          // generic Repository<T, Id, C>
@reom/adapter-ioredis     // bridge for ioredis
@reom/adapter-redis       // bridge for redis@4

# magic layer (opt‑in)
@reom/magic-autoschema    // @AutoModel() decorator – infer schema
@reom/magic-index         // @Indexed() + findBy() secondary SET index
@reom/magic-live          // live() → Observable<T> via keyspace notifications
```

Import only what you need – everything is tree‑shakable.

---

## 🚀 Quick Install (ioredis)

```bash
pnpm add @reom/core @reom/ports @reom/repository \
         @reom/adapter-ioredis ioredis
```

> Or simply `pnpm add reom` for the convenience meta‑package (includes common deps).

---

## ⚡ Quick Start

```ts
import Redis from "ioredis";
import { IoRedisAdapter } from "@reom/adapter-ioredis";
import { Repository, Model } from "@reom/repository";

@Model<User>("User", { prefix: "user", ttl: 3600 })
class User {
  constructor(
    public id: string,
    public profile: { name: string; tags: string[] },
    public active = true
  ) {}
}

const client = new IoRedisAdapter(new Redis());
const userRepo = new Repository<User>(client, "User");

await userRepo.save(new User("u123", { name: "Alice", tags: ["admin"] }));
const user = await userRepo.find("u123");
```

---

## 🔮 Add Magic (optional)

```bash
pnpm add @reom/magic-autoschema @reom/magic-index @reom/magic-live rxjs
```

```ts
import "reflect-metadata"; // required for decorators
import { AutoModel, Indexed } from "@reom/magic-autoschema";
import { live } from "@reom/magic-live";

@AutoModel()
class Article {
  id!: string;
  @Indexed() slug!: string;
  title!: string;
}

// Auto schema registered – no manual prefix needed
await articleRepo.findBy("slug", "hello-world");
live(articleRepo, "a1").subscribe(a => console.log(a));
```

---

## 🛠 CLI (coming soon)

* `reom import users.csv --into User`
* `reom inspect user:* --pretty`

---

## 📁 Project Structure (monorepo)

```
packages/
  core/
  ports/
  repository/
  adapter-ioredis/
  adapter-redis/
  magic-autoschema/
  magic-index/
  magic-live/
```

Built with **pnpm workspaces** & **tsup**.

---

## 📜 Licence

MIT © 2025 — Reom Contributors
