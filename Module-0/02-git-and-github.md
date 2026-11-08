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

## Step 9: Create our first Git commit

**Step 10 passed.** Django reports:

```text
System check identified no issues (0 silenced).
```

This confirms Django's system checks found no configuration problems. It does not yet mean all application tests have passed.

#### Step 11 — Create our first Git commit

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "chore: initialize Django project (module 0)"
```

**Understanding the command:**

- `git commit` — Saves a snapshot of the staged files in local Git history.
- `-m` — Specifies the commit message.
- `chore:` — Indicates project setup or maintenance work.
- `module 0` — Identifies the development stage.

**Important:** This creates a local commit only. Nothing becomes public until we push it to GitHub.

Send me the output. Next, we'll create the public GitHub repository named `ai-assisted-inventory-manager`. We'll also set up the separate private journal repository.

*Previous: Step 8 (Stage the project files)  |  Next: Step 10 (Connect your local project to GitHub)*

---

## Step 10: Connect your local project to GitHub

**Step 12 completed.** I've verified that your GitHub repository exists and is public.

ai-assisted-inventory-manager (https://github.com/mehreen-7547/ai-assisted-inventory-manager)

#### Step 13 — Connect your local project to GitHub

Run this in your existing VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**Understanding the command:**

- `git remote` — Manages connections to remote repositories.
- `add` — Creates a new connection.
- `origin` — The conventional name for your main remote repository.
- The URL — Identifies your public GitHub repository.

This command **only establishes the connection**. It does not upload your files.

Send me the output. Next, we'll verify the connection before pushing your first commit.

*Previous: Step 9 (Create our first Git commit)  |  Next: Step 11 (Connect your local project to GitHub)*

---

## Step 11: Connect your local project to GitHub

That's the correct public GitHub repository:

ai-assisted-inventory-manager (https://github.com/mehreen-7547/ai-assisted-inventory-manager)

#### Step 13 — Connect your local project to GitHub

In your existing VS Code terminal, run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

This connects your local Git repository to the GitHub repository.

**It does not upload anything yet.**

Send me the terminal output. Next, we'll verify the connection and push your first commit.

#### How we checked it

**Step 13 completed without errors.** Your local project is now configured to use GitHub as its remote repository.

#### Step 14 — Verify the remote connection

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote -v
```

**Understanding the command:**

- `git remote` — Shows configured remote repositories.
- `-v` — Displays their URLs and whether they're used for fetching or pushing.

**Expected output:**

```text
origin  https://github.com/mehreen-7547/ai-assisted-inventory-manager.git (fetch)
origin  https://github.com/mehreen-7547/ai-assisted-inventory-manager.git (push)
```

This checks the saved remote configuration; it doesn't contact GitHub or upload files.

Send me the output. Next, we'll push your first commit to the public repository.

*Previous: Step 10 (Connect your local project to GitHub)  |  Next: Step 12 (Push your first commit to GitHub)*

---

## Step 12: Push your first commit to GitHub

**Step 14 passed.** Your local Git repository is correctly connected to GitHub for both fetching and pushing.

#### Step 15 — Push your first commit to GitHub

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push -u origin main
```

**Understanding the command:**

- `git push` — Uploads local commits to GitHub.
- `-u` — Sets the upstream branch, so future pushes can simply use `git push`.
- `origin` — Your GitHub repository.
- `main` — The branch we're uploading.

**Important:** This is the first command that will publish your committed project files to your public GitHub repository.

Git may ask you to authenticate through your browser. Follow the prompts if they appear.

Send me the terminal output. We'll verify the upload before setting up your separate private development journal repository.

*Previous: Step 11 (Connect your local project to GitHub)  |  Next: Step 13 (Create a separate local journal folder)*

---

## Step 13: Create a separate local journal folder

**Step 16 completed.** I've verified that your development journal repository exists and is **private**.

inventory-development-journal (https://github.com/mehreen-7547/inventory-development-journal)

Your repositories are now:

| Repository | Visibility |
|---|---|
| `ai-assisted-inventory-manager` | Public |
| `inventory-development-journal` | Private |

#### Step 17 — Create a separate local journal folder

In your existing VS Code PowerShell terminal, run:

```powershell
New-Item -ItemType Directory -Path C:\Users\mehre\projects2026\inventory-development-journal
```

**Understanding the command:**
- `New-Item` — Creates a new item.
- `-ItemType Directory` — Specifies that we're creating a folder.
- `-Path` — Specifies its location.

Our local folders will be separate:

```text
projects2026/
├── Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│   └── techmindz_inventory_module0_sqlite_atomic/
│       └── Django project (public Git repository)
│
└── inventory-development-journal/
    └── Learning journal (private Git repository)
```

This separation prevents your private learning notes from accidentally entering your public project's Git history.

**Send me the command output.** Next, we'll connect this folder to the private GitHub repository and add the journal covering our completed setup steps.

*Previous: Step 12 (Push your first commit to GitHub)  |  Next: Step 14 (Initialize Git for the private journal)*

---

## Step 14: Initialize Git for the private journal

**Step 17 completed.** Your private journal folder has been created successfully.

#### Step 18 — Initialize Git for the private journal

Run this in your existing VS Code terminal:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal init -b main
```

**Understanding the command:**

- `git init` — Creates a new local Git repository.
- `-C` — Specifies the journal folder.
- `-b main` — Names its initial branch `main`.

**Why?** Your public Django project already has its own Git repository. We need a second, independent repository to track your private learning journal.

This command does not upload anything or change your public project.

Send me the output. Next, we'll connect this new local repository to your private GitHub repository.

*Previous: Step 13 (Create a separate local journal folder)  |  Next: Step 15 (Connect it to GitHub)*

---

## Step 15: Connect it to GitHub

**Step 18 completed successfully.** Your private journal now has its own local Git repository on the `main` branch.

#### Step 19 — Connect it to GitHub

Run this in your existing terminal:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal remote add origin https://github.com/mehreen-7547/inventory-development-journal.git
```

**Understanding the command:**

- `git remote add` — Registers a remote repository.
- `origin` — The conventional name for the main remote.
- The URL — Points to your **private** GitHub journal repository.

This command only saves the connection. It does not upload files or affect your public Django repository.

Send me the output. Next, we'll verify the connection and add your development journal.

#### How we checked it

#### Step 20 — Verify the private repository connection

Assuming you've completed Step 19, run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal remote -v
```

**What this does:** Displays the GitHub repository connected to your local journal folder.

**Expected output:**

```text
origin  https://github.com/mehreen-7547/inventory-development-journal.git (fetch)
origin  https://github.com/mehreen-7547/inventory-development-journal.git (push)
```

This command does not upload or modify anything.

Send me the output. Next, we'll add the development journal documenting everything completed so far.

#### How we checked it

#### Step 22 — Verify the journal file

Before committing, let's confirm that the journal is in your private repository folder.

Run:

```powershell
Get-ChildItem C:\Users\mehre\projects2026\inventory-development-journal -Name
```

**What this does:** Lists the files inside your private journal folder without modifying anything.

**Expected output:**

```text
inventory-development-journal.md
```

Send me the output. If the file is present, we'll stage it, commit it, and push it to your private GitHub repository.

*Previous: Step 14 (Initialize Git for the private journal)  |  Next: Step 16 (Stage the journal for Git)*

---

## Step 16: Stage the journal for Git

**Step 22 completed.** Your development journal file is present in the correct private repository folder.

#### Step 23 — Stage the journal for Git

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal add inventory-development-journal.md
```

**Understanding the command:**

- `git -C` — Runs Git inside your private journal folder.
- `add` — Stages a file for the next commit.
- `inventory-development-journal.md` — The specific file we're staging.

This does **not** upload anything to GitHub or affect your public Django repository.

Send me the terminal output. Next, we'll verify the staged file before creating our first private commit.

*Previous: Step 15 (Connect it to GitHub)  |  Next: Step 17 (Change views.py)*

---

## Step 17: Change views.py

**Staging means selecting which changes you want to include in your next Git commit.**

Think of Git as having three stages:

    **1. Working directory**

    Where you create and edit your files

  git add

    **2. Staging area**

    Changes selected for the next commit

  git commit

    **3. Local Git repository**

    A permanent snapshot recorded in Git history

  git push

    **4. GitHub**

    Your committed changes are uploaded online

#### Example from our project

Suppose you have three files: `views.py`, `models.py`, and `notes.md`.

If you run:

```bash
git add views.py
```

Only the current changes in `views.py` are staged. The other two files aren't included in the next commit unless you stage them too.

Then:

```bash
git commit -m "Add inventory views"
```

Git records a snapshot of the staged changes.

**Key distinction:** `git add` prepares changes, `git commit` records them locally, and `git push` uploads commits to GitHub.

In our current step, we're staging only `inventory-development-journal.md` in your **private repository**.

*Previous: Step 16 (Stage the journal for Git)  |  Next: Step 18 (Stage the private journal)*

---

## Step 18: Stage the private journal

#### Step 23 — Stage the private journal

Run this in your VS Code terminal:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal add inventory-development-journal.md
```

This selects the journal file for the next Git commit. It does not upload anything.

Send me the terminal output, and we'll verify the staged file before committing.

#### How we checked it

**Step 23 completed.** Your journal file has been staged successfully.

The warning is harmless:

- **LF** — Unix-style line endings.
- **CRLF** — Windows-style line endings.
- Git is informing you that it may convert the line endings when working with the file.

This does not mean your journal content is damaged.

#### Step 24 — Verify the staged file

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal diff --cached --name-only
```

**What this does:** Lists the files selected for the next commit.

**Expected output:**

```text
inventory-development-journal.md
```

This command makes no changes.

Send me the output. Then we'll create the first commit in your private repository.

*Previous: Step 17 (Change views.py)  |  Next: Step 19 (Commit the private development journal)*

---
