# Awesome Lists — PR Drafts

## awesome-nodejs (sindresorhus/awesome-nodejs)

**PR Title:** Add pg-safe-migrate to Database section

**Target file:** `readme.md` → find the "Database" section → "Other" or "Migrations" subsection

**Add this line** (alphabetical order):

```markdown
- [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) - Safety-first PostgreSQL migration engine with advisory locks, drift detection, and 10 built-in lint rules.
```

**PR Description:**

Adding [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) — a PostgreSQL migration engine for Node.js focused on safety defaults.

**Key features:**
- Advisory locks (`pg_advisory_lock`) to prevent concurrent migration runs
- SHA-256 checksum drift detection
- 10 built-in lint rules (DROP TABLE, missing CONCURRENTLY, ALTER TYPE in transactions, etc.)
- CLI + programmatic API + GitHub Action
- Works alongside existing ORMs (Prisma, Drizzle, Knex, TypeORM)

**Why it belongs here:**
- Published on npm: [pg-safe-migrate](https://www.npmjs.com/package/pg-safe-migrate)
- 153 tests, strict TypeScript, dual ESM/CJS, MIT licensed
- Fills a gap — no other Node.js migration tool combines advisory locks + drift detection + safety linting

**Steps to submit:**
1. Fork https://github.com/sindresorhus/awesome-nodejs
2. Edit `readme.md`
3. Find the **Database** → **Other** section
4. Add the line above in alphabetical order
5. Open PR with the title and description above

---

## awesome-postgres (dhamaniasad/awesome-postgres)

**PR Title:** Add pg-safe-migrate to Utilities/Migration Tools section

**Add this line:**

```markdown
- [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) - Safety-first Node.js migration engine with advisory locks, SHA-256 drift detection, and 10 built-in lint rules for PostgreSQL.
```

**PR Description:**

Adding [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) — a Node.js migration CLI and library specifically designed around PostgreSQL safety primitives.

**What makes it PostgreSQL-specific:**
- Uses `pg_advisory_lock` for concurrency control
- Lint rules targeting PG-specific gotchas: `CREATE INDEX CONCURRENTLY`, `ALTER TYPE ADD VALUE` in transactions, `SET NOT NULL` without check constraints
- Auto-detects `CONCURRENTLY` and runs outside transactions
- SHA-256 checksums for drift detection

Published on npm, 153 tests, MIT licensed.

**Steps to submit:**
1. Fork https://github.com/dhamaniasad/awesome-postgres
2. Edit `README.md`
3. Find the **Utilities / Migration tools** section (or closest match)
4. Add the line above
5. Open PR

---

## awesome-typescript (dzharii/awesome-typescript)

**PR Title:** Add pg-safe-migrate to Database Tools section

**Add this line:**

```markdown
- [pg-safe-migrate](https://github.com/defnotwig/pg-safe-migrate) - Safety-first PostgreSQL migration engine with strict TypeScript, zero `any` types, and full type exports.
```

**Steps to submit:**
1. Fork https://github.com/dzharii/awesome-typescript
2. Find the appropriate section
3. Add the line
4. Open PR
