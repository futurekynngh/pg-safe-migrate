# nextjs-postgres-starter

> Next.js + PostgreSQL starter template with pg-safe-migrate for safe schema migrations.

## Why migrations fail in prod

Most Postgres outages from migrations happen for three reasons:

1. **Missing `CONCURRENTLY`** on index creation → locks the whole table
2. **No advisory locks** → two deploys run migrations simultaneously → corrupted state
3. **No drift detection** → someone hand-edits a migration → silent schema mismatch

This template prewires [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) to prevent all three.
It also includes an example of a `CONCURRENTLY` index migration to demonstrate the value.

## Quick Start

```bash
# Clone and install
git clone https://github.com/defnotwig/nextjs-postgres-starter.git
cd nextjs-postgres-starter
pnpm install

# Start Postgres
docker compose up -d

# Apply migrations
pnpm db:up

# Start Next.js dev server
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000).

## Stack

- **Next.js 14** — App Router with Route Handlers
- **pg** (node-postgres) — PostgreSQL client
- **pg-safe-migrate** — Migration engine with safety linting
- **Docker Compose** — Local Postgres
- **TypeScript** — Strict mode
- **GitHub Actions** — CI with migration check gate

## Scripts

| Script           | Description                          |
| ---------------- | ------------------------------------ |
| `pnpm dev`       | Start Next.js dev server             |
| `pnpm build`     | Production build                     |
| `pnpm start`     | Start production server              |
| `pnpm db:up`     | Apply pending migrations             |
| `pnpm db:down`   | Rollback last migration              |
| `pnpm db:status` | Show migration status                |
| `pnpm db:lint`   | Lint migrations for safety issues    |
| `pnpm db:check`  | Full check (lint + drift + ordering) |
| `pnpm db:create` | Create a new migration               |

## Project Structure

```
├── migrations/
│   ├── 0001_create-posts.up.sql
│   ├── 0001_create-posts.down.sql
│   ├── 0002_add-posts-slug-index.up.sql     ← CONCURRENTLY example
│   └── 0002_add-posts-slug-index.down.sql
├── src/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── api/
│   │       └── posts/
│   │           └── route.ts
│   └── lib/
│       └── db.ts
├── docker-compose.yml
├── pgsm.config.json
└── package.json
```

## CONCURRENTLY Index Migration

Migration `0002` demonstrates how to safely add an index on a production table
without locking it:

```sql
-- pgsm-transaction: never
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_posts_slug ON posts (slug);
```

The `-- pgsm-transaction: never` directive tells pg-safe-migrate to run this
outside a transaction (required by PostgreSQL for `CONCURRENTLY`).

## CI

Pull requests are automatically checked for migration safety issues.
See `.github/workflows/ci.yml`.

[![CI](https://github.com/defnotwig/nextjs-postgres-starter/actions/workflows/ci.yml/badge.svg)](https://github.com/defnotwig/nextjs-postgres-starter/actions)

## License

MIT
