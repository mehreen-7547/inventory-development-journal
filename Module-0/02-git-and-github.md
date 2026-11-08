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

## Step 8: Stage the project files

**Verified. Your `.env.example` is safe to include in the public repository.**

- The secret key is a placeholder, not your actual key.
- No database passwords are present.
- SQLite is configured as agreed.
- MySQL settings are included for possible future use, without enabling MySQL.

#### Step 8 — Stage the project files

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add .
```

**Understanding the command:**

- `git add` — Prepares files for the next commit.
- `.` — Includes all eligible files within the repository.
- Git respects the `.gitignore` rules we checked.

**Important:** Staging does not upload anything to GitHub or make anything public.

After this, we'll inspect the staged files and run Django's checks before creating our first commit.

Send me the terminal output. An empty output usually means the command succeeded.

#### How we checked it

**Git staging completed without errors.** The empty output is normal.

Your files are now prepared for the first commit, but nothing has been committed or uploaded to GitHub.

#### Step 9 — Verify the staged files

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic diff --cached --name-only
```

**Understanding the command:**

- `git diff` — Examines changes.
- `--cached` — Looks specifically at staged changes.
- `--name-only` — Displays filenames without their contents.

**Why are we doing this?** Before committing to a public repository, we want to confirm that `.env`, passwords, and private journal files are not included.

This command only displays information. It doesn't change anything.

Send me the output. We'll review it, then run Django's checks before our first commit.

#### How we checked it

**Step 9 passed.** All 20 staged files belong to the Django project.

- `.env` and `.venv` are excluded.
- The private development journal is not included.
- No unexpected files appear in the list.

Nothing has been committed or uploaded yet.

#### Step 10 — Check Django's configuration

Run:

```powershell
.\.venv\Scripts\python.exe .\techmindz_inventory_module0_sqlite_atomic\manage.py check
```

**Understanding the command:**

- `python.exe` — Uses Python from your virtual environment.
- `manage.py` — Django's project management script.
- `check` — Runs Django's built-in system checks for configuration problems.

**Expected output:**

```text
System check identified no issues (0 silenced).
```

This does not modify your database.

Send me the output. After verification, we'll proceed toward our first Git commit for `ai-assisted-inventory-manager`.

*Previous: Step 7 (Initialize the Git repository)  |  Next: Step 9 (Create our first Git commit)*

---
