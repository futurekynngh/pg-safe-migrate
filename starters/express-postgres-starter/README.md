# express-postgres-starter

> Express + PostgreSQL starter template with pg-safe-migrate for safe schema migrations.

## Why migrations fail in prod

Most Postgres outages from migrations happen for three reasons:

1. **Missing `CONCURRENTLY`** on index creation → locks the whole table
2. **No advisory locks** → two deploys run migrations simultaneously → corrupted state
3. **No drift detection** → someone hand-edits a migration → silent schema mismatch

This template prewires [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) to prevent all three.

## Quick Start

```bash
# Clone and install
git clone https://github.com/defnotwig/express-postgres-starter.git
cd express-postgres-starter
pnpm install

# Start Postgres
docker compose up -d

# Apply migrations
pnpm db:up

# Start the server
pnpm dev
```

## Stack

- **Express** — HTTP server
- **pg** (node-postgres) — PostgreSQL client
- **pg-safe-migrate** — Migration engine with safety linting
- **Docker Compose** — Local Postgres
- **TypeScript** — Strict mode
- **GitHub Actions** — CI with migration check gate

## Scripts

| Script           | Description                          |
| ---------------- | ------------------------------------ |
| `pnpm dev`       | Start dev server with watch          |
| `pnpm build`     | Compile TypeScript                   |
| `pnpm start`     | Start production server              |
| `pnpm db:up`     | Apply pending migrations             |
| `pnpm db:down`   | Rollback last migration              |
| `pnpm db:status` | Show migration status                |
| `pnpm db:lint`   | Lint migrations for safety issues    |
| `pnpm db:check`  | Full check (lint + drift + ordering) |
| `pnpm db:create` | Create a new migration               |
| `pnpm test`      | Run tests                            |

## Project Structure

```
├── migrations/
│   ├── 0001_create-users.up.sql
│   ├── 0001_create-users.down.sql
│   ├── 0002_create-sessions.up.sql
│   └── 0002_create-sessions.down.sql
├── src/
│   ├── app.ts
│   ├── db.ts
│   └── routes/
│       └── users.ts
├── docker-compose.yml
├── pgsm.config.json
├── tsconfig.json
└── package.json
```

## CI

Pull requests are automatically checked for migration safety issues.
See `.github/workflows/ci.yml`.

[![CI](https://github.com/defnotwig/express-postgres-starter/actions/workflows/ci.yml/badge.svg)](https://github.com/defnotwig/express-postgres-starter/actions)

## License

MIT
