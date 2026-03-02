---
title: "pg-safe-migrate: Advisory Locks + Drift Detection + Safe Defaults for Node.js PostgreSQL Migrations"
published: true
description: "A safety-first PostgreSQL migration engine that prevents the 3 most common Postgres migration incidents: table locks, concurrent deploy races, and silent schema drift."
tags: postgresql, node, typescript, database
canonical_url: https://github.com/defnotwig/pg-safe-migrate
cover_image:
series:
---

> A safety-first PostgreSQL migration engine for Node.js.

**TL;DR:** [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) is a new open-source migration tool that prevents the three most common Postgres migration incidents: table locks from missing `CONCURRENTLY`, race conditions from concurrent deploys, and silent schema drift from edited migration files.

## The Problem

Every team that runs PostgreSQL in production eventually hits one of these:

**1. The 2am lock incident.** Someone adds an index without `CONCURRENTLY`. The `CREATE INDEX` statement locks the table. Every query queues behind it. The app goes down.

**2. The double-deploy race.** Two CI pipelines trigger simultaneously. Both try to apply the same migration. One fails with a cryptic Postgres error. The migration table is now in an inconsistent state.

**3. The phantom drift.** A developer edits a migration file after it was applied. The database schema no longer matches what the code thinks it is. Bugs appear weeks later with no obvious cause.

These aren't edge cases — they're the top three incident classes in any Postgres post-mortem database.

## What pg-safe-migrate Does

pg-safe-migrate is a Node.js migration engine (CLI + library) that treats these as **first-class failure modes**.

### 10 Built-in Safety Rules

Every migration is linted before it runs:

```bash
$ npx pg-safe-migrate lint

PGSM003 [ERROR] CREATE INDEX without CONCURRENTLY
  → migrations/0005_add-email-index.up.sql:3
  Fix: Use CREATE INDEX CONCURRENTLY IF NOT EXISTS ...
  Override: -- pgsm:allow PGSM003 reason="small table, <1000 rows"
```

Rules include:

- **PGSM001**: Destructive `DROP TABLE` / `DROP COLUMN`
- **PGSM003**: Index creation without `CONCURRENTLY`
- **PGSM005**: `ALTER TYPE ADD VALUE` inside a transaction (PG limitation)
- **PGSM007**: `SET NOT NULL` without a check constraint (full table scan)
- **PGSM010**: `UPDATE`/`DELETE` without `WHERE` clause

### Advisory Locks

Every migration run acquires a PostgreSQL advisory lock first. If another process is already migrating, the second one waits (or fails fast). No more race conditions.

### SHA-256 Drift Detection

Every applied migration is checksummed. If the file on disk doesn't match what was applied, `check` and `doctor` will catch it:

```bash
$ npx pg-safe-migrate check

✗ Drift detected in 0003_add-posts.up.sql
  Applied checksum: a1b2c3...
  Current checksum: d4e5f6...
```

### Transaction Policy

Migrations run in transactions by default. But `CONCURRENTLY` can't run in a transaction — opt out per migration:

```sql
-- pgsm-transaction: never
CREATE INDEX CONCURRENTLY IF NOT EXISTS idx_users_email ON users (email);
```

### Override System

Every rule can be overridden with a required reason:

```sql
-- pgsm:allow PGSM001 reason="Removing deprecated feature flag table"
DROP TABLE IF EXISTS feature_flags;
```

## Quick Start

```bash
npm install -D pg-safe-migrate
npx pg-safe-migrate init
npx pg-safe-migrate create add-users-table
npx pg-safe-migrate lint     # Check for safety issues
npx pg-safe-migrate up       # Apply migrations
npx pg-safe-migrate doctor   # Full health check
```

## CI Gate (GitHub Action)

```yaml
- uses: defnotwig/pg-safe-migrate@v1
  with:
    command: check
    database-url: ${{ secrets.DATABASE_URL }}
```

Blocks PRs that introduce unsafe migrations, drift, or ordering issues.

## Comparison

| Feature             | pg-safe-migrate  | node-pg-migrate | graphile-migrate | dbmate |
| ------------------- | :--------------: | :-------------: | :--------------: | :----: |
| Safety linting      |   ✅ 10 rules    |       ❌        |        ❌        |   ❌   |
| Advisory locks      |        ✅        |       ❌        |        ✅        |   ❌   |
| Drift detection     |    ✅ SHA-256    |       ❌        |        ❌        |   ❌   |
| Transaction control | ✅ per-migration |       ✅        |        ✅        |   ✅   |
| Override with audit |        ✅        |       ❌        |        ❌        |   ❌   |
| GitHub Action       |        ✅        |       ❌        |        ❌        |   ❌   |

## Links

- **GitHub:** [defnotwig/pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate)
- **npm:** [pg-safe-migrate](https://www.npmjs.com/package/pg-safe-migrate)

MIT licensed. [Contributions welcome](https://github.com/defnotwig/pg-safe-migrate/blob/main/CONTRIBUTING.md).

---

_What safety checks do you wish your migration tool had? Let me know in the comments._
