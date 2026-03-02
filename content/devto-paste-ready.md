---
title: pg-safe-migrate — Stop Shipping Unsafe Postgres Migrations
published: false
description: A safety-first PostgreSQL migration engine that catches table locks, deploy races, and schema drift before they hit production.
tags: postgresql, node, typescript, database
---

> A safety-first PostgreSQL migration engine for Node.js.

**TL;DR:** [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) catches the 3 most common Postgres migration disasters — table locks, deploy races, and schema drift — before they reach production.

---

## The Problem

Every team running Postgres in production eventually hits one of these:

### 1. The 2am Lock Incident

Someone adds an index without `CONCURRENTLY`. The table locks. Every query queues behind it. The app goes down.

### 2. The Double-Deploy Race

Two CI pipelines trigger at once. Both try to apply the same migration. One fails. The migration table is now broken.

### 3. The Phantom Drift

A developer edits a migration after it ran. The database no longer matches what the code expects. Bugs show up weeks later.

---

## How pg-safe-migrate Fixes This

### Safety Linter (10 Rules)

Every migration is checked before it runs:

```
$ npx pg-safe-migrate lint

PGSM003 [ERROR] CREATE INDEX without CONCURRENTLY
  → migrations/0005_add-email-index.up.sql:3
  Fix: Use CREATE INDEX CONCURRENTLY IF NOT EXISTS
```

Key rules:

- `PGSM001` — Blocks `DROP TABLE` / `DROP COLUMN`
- `PGSM003` — Requires `CONCURRENTLY` for index creation
- `PGSM005` — Catches `ALTER TYPE ADD VALUE` in transactions
- `PGSM007` — Flags `SET NOT NULL` without check constraint
- `PGSM010` — Blocks `UPDATE`/`DELETE` without `WHERE`

### Advisory Locks

Every migration acquires a PostgreSQL advisory lock first. Second process waits or fails fast. No more race conditions.

### SHA-256 Drift Detection

Every applied migration is checksummed. If the file changes after applying:

```
$ npx pg-safe-migrate check

✗ Drift detected in 0003_add-posts.up.sql
  Applied: a1b2c3...
  Current: d4e5f6...
```

### Per-Migration Transaction Control

`CONCURRENTLY` can't run inside a transaction. Override per file:

```sql
-- pgsm-transaction: never
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_email ON users (email);
```

### Auditable Overrides

Override any rule with a required reason:

```sql
-- pgsm:allow PGSM001 reason="Removing deprecated feature flag table"
DROP TABLE IF EXISTS feature_flags;
```

---

## Quick Start

```bash
npm install -D pg-safe-migrate
npx pg-safe-migrate init
npx pg-safe-migrate create add-users-table
npx pg-safe-migrate lint
npx pg-safe-migrate up
npx pg-safe-migrate doctor
```

## CI Gate

```yaml
- uses: defnotwig/pg-safe-migrate@v1
  with:
    command: check
    database-url: ${{ secrets.DATABASE_URL }}
```

Blocks PRs with unsafe migrations or drift.

---

## How It Compares

| Feature         | pg-safe-migrate | node-pg-migrate | graphile-migrate | dbmate |
| --------------- | --------------- | --------------- | ---------------- | ------ |
| Safety linting  | 10 rules        | No              | No               | No     |
| Advisory locks  | Yes             | No              | Yes              | No     |
| Drift detection | SHA-256         | No              | No               | No     |
| Override system | Auditable       | No              | No               | No     |
| GitHub Action   | Yes             | No              | No               | No     |

---

## Links

- [GitHub](https://github.com/defnotwig/pg-safe-migrate)
- [npm](https://www.npmjs.com/package/pg-safe-migrate)
- [Contributing Guide](https://github.com/defnotwig/pg-safe-migrate/blob/main/CONTRIBUTING.md)

MIT licensed. PRs welcome.

_What safety checks do you wish your migration tool had?_
