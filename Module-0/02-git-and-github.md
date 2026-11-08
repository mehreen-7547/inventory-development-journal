# Module 0, Chapter 2: Git and GitHub

## Step 7: Initialize the Git repository

This is expected. It means **Git has not yet been initialized** in your Django project.

#### Step 5 — Initialize the Git repository

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic init -b main
```

**Understanding the command:**

- `git init` — Creates a local Git repository.
- `-C` — Specifies your Django project folder.
- `-b main` — Names the initial branch `main`.

**What happens?** Git creates a hidden `.git` folder to store version history.

This does **not** upload anything to GitHub or make your files public.

Our GitHub repository will be named `ai-assisted-inventory-manager`, as you requested.

Run the command and send me the output. We'll then verify which files Git is preparing to track before making the first commit.

#### How we checked it

**Git initialization was successful.** Your Django project now has a local Git repository with `main` as its initial branch.

#### Step 6 — Check which files Git can track

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status --short --untracked-files=all
```

**Understanding the command:**

- `status` — Checks the repository's current state.
- `--short` — Displays a compact list.
- `--untracked-files=all` — Lists individual files Git hasn't started tracking.

You may see `??` beside filenames. This means they're **untracked**, not that anything is wrong.

This command does not change or upload files.

**Send me the output.** We'll review it before staging anything, ensuring that no secrets or private journal files enter the public repository.

#### How we checked it

**The file list looks correct.** Git has identified 19 project files, and none has been committed or uploaded yet.

Importantly:
- `.env` is not listed — your secret key file is excluded.
- `.venv` is not listed — installed dependencies are excluded.
- Your private development journal is not listed.
- `.env.example` is included — this is normal, provided it contains only placeholder values.

#### Step 7 — Verify `.env.example`

Before our first public commit, run:

```powershell
Get-Content .\techmindz_inventory_module0_sqlite_atomic\.env.example
```

**Why?** `.env.example` is intended to show other developers which configuration variables they need, without exposing actual passwords or secret keys.

This command only displays the file; it makes no changes.

Send me the output, but redact any real passwords or secret keys if present. Then we'll proceed toward our first commit.

*Previous: Step 6 (Create .env)  |  Next: Step 8 (Stage the project files)*

---
