# Reddit Post Drafts

## r/node

**Title:** I built a safety-first PostgreSQL migration engine for Node.js — advisory locks, drift detection, 10 lint rules

**Body:**

Hey r/node,

I've been working on **pg-safe-migrate** — a migration tool that tries to prevent the most common Postgres migration incidents before they happen.

**The problem it solves:**

1. **Race conditions** — Two deploys run simultaneously, both try to migrate. Without advisory locks, you get a corrupted migration table.
2. **Table locks** — Someone creates an index without `CONCURRENTLY`. The table locks, queries queue, app goes down.
3. **Schema drift** — A developer edits a migration file after it was applied. The database no longer matches what was applied.

**What it does:**

- **Advisory locks** (pg_advisory_lock) — prevents concurrent migration runs
- **SHA-256 drift detection** — catches modified migration files
- **10 lint rules** — catches DROP TABLE, missing CONCURRENTLY, ALTER TYPE in transactions, etc.
- **Transaction policy** — auto-detects CONCURRENTLY and runs outside transactions
- **Override system** — `-- pgsm:allow PGSM003 reason="small table"` with audit trail
- **CLI + Library + GitHub Action**

Works alongside Prisma, Drizzle, Knex, TypeORM — it handles the migration safety layer, you keep your preferred query builder.

```bash
npm install -D pg-safe-migrate
npx pg-safe-migrate init
npx pg-safe-migrate create add-users
npx pg-safe-migrate lint
npx pg-safe-migrate up
```

**Links:**

- GitHub: https://github.com/defnotwig/pg-safe-migrate
- npm: https://www.npmjs.com/package/pg-safe-migrate

153 tests, strict TypeScript, ESM + CJS, MIT licensed. Would love feedback on the lint rules or any edge cases I'm missing.

---

## r/PostgreSQL

**Title:** pg-safe-migrate: Node.js migration CLI with advisory locks, SHA-256 drift detection, and safe-defaults linting

**Body:**

Hi r/PostgreSQL,

I built a Node.js migration tool specifically designed around PostgreSQL safety primitives:

**Advisory locks:** Uses `pg_advisory_lock` with SHA-256-derived lock IDs to prevent concurrent migration runs. If two CI pipelines trigger at the same time, the second one waits.

**Drift detection:** Every applied migration gets a SHA-256 checksum. The `check` command compares disk vs. database and flags any file that was modified after application.

**10 safety rules (the interesting part for this sub):**

| Rule    | What it catches                                        |
| ------- | ------------------------------------------------------ |
| PGSM001 | DROP TABLE/COLUMN without explicit override            |
| PGSM003 | CREATE INDEX without CONCURRENTLY                      |
| PGSM004 | ALTER TABLE ... ADD COLUMN ... DEFAULT on large tables |
| PGSM005 | ALTER TYPE ADD VALUE inside a transaction              |
| PGSM006 | Missing IF NOT EXISTS / IF EXISTS guards               |
| PGSM007 | SET NOT NULL without prior check constraint            |
| PGSM008 | SET lock_timeout / statement_timeout in migrations     |
| PGSM010 | UPDATE/DELETE without WHERE clause                     |

Every rule can be overridden with a reason and optional Jira ticket:

```sql
-- pgsm:allow PGSM001 reason="Removing deprecated table" ticket="ENG-1234"
DROP TABLE IF EXISTS feature_flags;
```

**Transaction policy auto-detection:** If a migration contains `CONCURRENTLY`, it automatically runs outside a transaction. You can also force `always` or `never` per-migration.

Written in TypeScript, works as CLI or library. Pure SQL migration files (no JS/TS migration functions).

GitHub: https://github.com/defnotwig/pg-safe-migrate

Curious what safety rules you'd add — I know there are more PG-specific gotchas I'm probably missing.

---

## r/typescript

**Title:** pg-safe-migrate — Strict TypeScript PostgreSQL migration engine (0 `any` types, ESM+CJS, full type exports)

**Body:**

Just published **pg-safe-migrate** — a PostgreSQL migration engine written in strict TypeScript.

A few things TypeScript folks might appreciate:

- **Zero `any` types** across the entire codebase
- **Dual ESM + CJS builds** with `.d.ts` + `.d.cts` declarations
- **Full type exports** — `MigratorConfig`, `MigrationStep`, `LintIssue`, `DoctorResult`, etc.
- **Programmatic API** — not just a CLI, the core library is usable as a typed Node.js module

```typescript
import { createMigrator } from "pg-safe-migrate-core";
import type { MigratorConfig, MigrationPlan } from "pg-safe-migrate-core";

const config: MigratorConfig = {
  databaseUrl: process.env.DATABASE_URL!,
  migrationsDir: "./migrations",
};

const migrator = createMigrator(config);
const plan: MigrationPlan = await migrator.plan();
const issues = await migrator.lint();
const result = await migrator.check();
```

153 tests, 10 lint rules for PostgreSQL safety, advisory lock concurrency control, SHA-256 drift detection.

- GitHub: https://github.com/defnotwig/pg-safe-migrate
- npm: https://www.npmjs.com/package/pg-safe-migrate-core

MIT licensed.
