# MODULE 0 — Foundations

## Chapter 2: Git and GitHub (Steps 7–24)

**Where we are.** A working project is easy to lose if there is no version history. We set up Git for both the public code and a separate private development journal.

**What this chapter gives us.** Understand staging, commits, remotes, branches, and pushes without mixing the two repositories.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain the difference between git add, git commit, and git push.

### Find a step

- [Step 7: Initialize the Git repository](#step-7)
- [Step 8: Stage the project files](#step-8)
- [Step 9: Create our first Git commit](#step-9)
- [Step 10: Connect your local project to GitHub](#step-10)
- [Step 11: Connect your local project to GitHub](#step-11)
- [Step 12: Push your first commit to GitHub](#step-12)
- [Step 13: Create a separate local journal folder](#step-13)
- [Step 14: Initialize Git for the private journal](#step-14)
- [Step 15: Connect it to GitHub](#step-15)
- [Step 16: Stage the journal for Git](#step-16)
- [Step 17: Understand staging versus committing](#step-17)
- [Step 18: Stage the private journal](#step-18)
- [Step 19: Commit the private development journal](#step-19)
- [Step 20: Push your journal to private GitHub](#step-20)
- [Step 21: Understand local and remote main branches](#step-21)
- [Step 22: Apply database migrations](#step-22)
- [Step 23: Update your private development journal](#step-23)
- [Step 24: Stage the updated journal](#step-24)

---

<a id="step-7"></a>

### Step 7: Initialize the Git repository

**Why we are doing this.** After the starter project worked locally, we needed version control so changes could be recorded and recovered.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic init -b main
```

**How it works.** `git init -b main` creates a local `.git` history with `main` as the initial branch. At this point the files are not uploaded to GitHub.

**How we checked it.** The initial response indicated Git was not initialized; the next step proceeded to staging, consistent with initialization.

**Connects to.** Previous: [Step 6 — Create .env](01-environment-setup.md#step-6) · Next: [Step 8 — Stage the project files](02-git-and-github.md#step-8)


---

<a id="step-8"></a>

### Step 8: Stage the project files

**Why we are doing this.** Git does not automatically place every edited file in the next commit. We first selected the project files to record.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add .
```

**How it works.** `git add .` moves current changes into Git's staging area. It does not create a commit or publish anything; `.gitignore` must exclude the real `.env`, local database, and environment files.

**How we checked it.** The notes explicitly verified `.env.example` contained placeholders, but a final staged-file audit was not reproduced here.

**Connects to.** Previous: [Step 7 — Initialize the Git repository](02-git-and-github.md#step-7) · Next: [Step 9 — Create our first Git commit](02-git-and-github.md#step-9)


---

<a id="step-9"></a>

### Step 9: Create our first Git commit

**Why we are doing this.** With the intended starter files staged, we created a named restore point for the initial Django setup.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded example/output — **do not type this as an instruction**.*

```text
System check identified no issues (0 silenced).
```

*2. Recorded snippet; Run.*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "chore: initialize Django project (module 0)"
```

**How it works.** `git commit -m ...` saves staged changes locally. The message identifies the purpose of that snapshot; it does not upload code by itself.

**How we checked it.** The source records Django's system check returned zero configuration issues before the commit. That is not a complete application test.

**Connects to.** Previous: [Step 8 — Stage the project files](02-git-and-github.md#step-8) · Next: [Step 10 — Connect your local project to GitHub](02-git-and-github.md#step-10)


---

<a id="step-10"></a>

### Step 10: Connect your local project to GitHub

**Why we are doing this.** The local repository needed a destination for its first public backup of the application code.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your existing VS Code terminal (historical; **do not rerun blindly**).*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**How it works.** `git remote add origin ...` records the GitHub repository URL under the name `origin`. It does not upload files.

**How we checked it.** The original notes refer to a public application repository. Connection and pushing were addressed in subsequent steps.

**Connects to.** Previous: [Step 9 — Create our first Git commit](02-git-and-github.md#step-9) · Next: [Step 11 — Connect your local project to GitHub](02-git-and-github.md#step-11)


---

<a id="step-11"></a>

### Step 11: Connect your local project to GitHub

**Why we are doing this.** We revisited the remote setup to be certain we were linking the Django application, not the separate private study journal.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In your existing VS Code terminal, run (historical; **do not rerun blindly**).*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**How it works.** The repeated `remote add origin` command is an alternative attempt at the same operation, not a second remote requirement. If `origin` already exists, inspect it rather than blindly adding it again.

**How we checked it.** The later note says the remote connection was verified. This historical repeat should not be executed twice.

**Connects to.** Previous: [Step 10 — Connect your local project to GitHub](02-git-and-github.md#step-10) · Next: [Step 12 — Push your first commit to GitHub](02-git-and-github.md#step-12)


---

<a id="step-12"></a>

### Step 12: Push your first commit to GitHub

**Why we are doing this.** After committing locally and connecting `origin`, the application needed to be uploaded to GitHub.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push -u origin main
```

**How it works.** `git push -u origin main` sends the local `main` commits to the remote. `-u` remembers the upstream so later pushes need fewer arguments.

**How we checked it.** The next note confirms the public application repository and separate private journal repository existed.

**Connects to.** Previous: [Step 11 — Connect your local project to GitHub](02-git-and-github.md#step-11) · Next: [Step 13 — Create a separate local journal folder](02-git-and-github.md#step-13)


---

<a id="step-13"></a>

### Step 13: Create a separate local journal folder

**Why we are doing this.** We decided to keep learning notes separate from the application source, because the journal may contain detailed implementation commentary.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In your existing VS Code PowerShell terminal, run.*

```powershell
New-Item -ItemType Directory -Path C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal
```

*2. Recorded example/output — **do not type this as an instruction**.*

```text
projects2026/
├── Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│   └── techmindz_inventory_module0_sqlite_atomic/
│       └── Django project (public Git repository)
│
└── inventory-development-journal/
    └── Learning journal (private Git repository)
```

**How it works.** `New-Item -ItemType Directory` creates a local folder for a second repository. It is not inside the Django application's Git history.

**How we checked it.** The next step says the private journal directory had been created successfully.

**Connects to.** Previous: [Step 12 — Push your first commit to GitHub](02-git-and-github.md#step-12) · Next: [Step 14 — Initialize Git for the private journal](02-git-and-github.md#step-14)


---

<a id="step-14"></a>

### Step 14: Initialize Git for the private journal

**Why we are doing this.** The private journal needed its own independent version history before it could be synchronized with GitHub.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your existing VS Code terminal (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal init -b main
```

**How it works.** Running `git init -b main` inside the journal folder initializes a separate repository. Two repositories can both have a branch named `main` without conflict.

**How we checked it.** The source confirms the private journal was initialized on `main`.

**Connects to.** Previous: [Step 13 — Create a separate local journal folder](02-git-and-github.md#step-13) · Next: [Step 15 — Connect it to GitHub](02-git-and-github.md#step-15)


---

<a id="step-15"></a>

### Step 15: Connect it to GitHub

**Why we are doing this.** The local journal directory was not yet connected to the private GitHub repository where we wanted to store notes.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your existing terminal (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal remote add origin https://github.com/mehreen-7547/inventory-development-journal.git
```

**How it works.** `git remote add origin ...` tells this second repository where to push and fetch. Its URL differs from the public application repository.

**How we checked it.** A later check confirmed the journal remote, although this step was partially repeated during setup.

**Connects to.** Previous: [Step 14 — Initialize Git for the private journal](02-git-and-github.md#step-14) · Next: [Step 16 — Stage the journal for Git](02-git-and-github.md#step-16)


---

<a id="step-16"></a>

### Step 16: Stage the journal for Git

**Why we are doing this.** The first journal entry existed locally, but Git would ignore it until it was staged.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** The `git add` command names the Markdown journal file and selects its current contents for the next commit. Staging is still local.

**How we checked it.** The notes show a subsequent repetition of staging, so this entry describes the intended action rather than a distinct feature.

**Connects to.** Previous: [Step 15 — Connect it to GitHub](02-git-and-github.md#step-15) · Next: [Step 17 — Understand staging versus committing](02-git-and-github.md#step-17)


---

<a id="step-17"></a>

### Step 17: Understand staging versus committing

**Why we are doing this.** During the Git discussion, we clarified the difference between editing a code file and recording that edit. This was a teaching example, not a new inventory view.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```bash
git commit -m "Add inventory views"
```

**How it works.** `git add views.py` is an example of staging one file; `git commit` is the separate action that saves it. The example must not be mistaken for proof that `views.py` was modified here.

**How we checked it.** No Django view was created in this step. The note was explanatory only.

**Connects to.** Previous: [Step 16 — Stage the journal for Git](02-git-and-github.md#step-16) · Next: [Step 18 — Stage the private journal](02-git-and-github.md#step-18)


---

<a id="step-18"></a>

### Step 18: Stage the private journal

**Why we are doing this.** We returned from the Git example to the real private journal file and prepared its first saved revision.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your VS Code terminal.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** This repeat of `git add` points to the journal Markdown file. It makes the next commit include the current notes, not the application code.

**How we checked it.** The following step shows the intended local commit operation.

**Connects to.** Previous: [Step 17 — Understand staging versus committing](02-git-and-github.md#step-17) · Next: [Step 19 — Commit the private development journal](02-git-and-github.md#step-19)


---

<a id="step-19"></a>

### Step 19: Commit the private development journal

**Why we are doing this.** Once the journal file was staged, we recorded it as a local commit so the first notes had a stable history.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 24 showed only inventory-development-journal.md, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: add initial development and setup journal"
```

**How it works.** `git commit` groups staged content with a description. Unlike `push`, it works without network access.

**How we checked it.** The record then proceeds to publishing the journal, indicating the local commit workflow was followed.

**Connects to.** Previous: [Step 18 — Stage the private journal](02-git-and-github.md#step-18) · Next: [Step 20 — Push your journal to private GitHub](02-git-and-github.md#step-20)


---

<a id="step-20"></a>

### Step 20: Push your journal to private GitHub

**Why we are doing this.** The journal's first local commit needed to be copied to the private remote repository.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming the remote URL from Step 26 was correct, run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push -u origin main
```

**How it works.** `git push -u origin main` publishes the journal branch and configures tracking. This does not affect the application repository.

**How we checked it.** Later source notes refer to the private repository as successfully synchronized.

**Connects to.** Previous: [Step 19 — Commit the private development journal](02-git-and-github.md#step-19) · Next: [Step 21 — Understand local and remote main branches](02-git-and-github.md#step-21)


---

<a id="step-21"></a>

### Step 21: Understand local and remote main branches

**Why we are doing this.** We clarified how local and remote branches relate because both repositories used a branch named `main`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; In our command (historical; **do not rerun blindly**).*

```powershell
git push -u origin main
```

**How it works.** A branch is a named sequence of commits within one repository. `git push -u` links local `main` to remote `main`; later `git push` uses that link.

**How we checked it.** This was a conceptual explanation. No additional file change or standalone test occurred.

**Connects to.** Previous: [Step 20 — Push your journal to private GitHub](02-git-and-github.md#step-20) · Next: [Step 22 — Apply database migrations](02-git-and-github.md#step-22)


---

<a id="step-22"></a>

### Step 22: Apply database migrations

**Why we are doing this.** Django was installed, but its built-in account, session, and admin tables had not yet been created in SQLite.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py migrate
```

**How it works.** `manage.py migrate` executes unapplied database migrations. A migration describes a schema change; running it changes the database.

**Follow the flow.** Django keeps migration files as a record of schema changes. `migrate` checks which ones are missing and runs those changes against SQLite. This is why seeing migration files in the repository is not enough: the local database needs to apply them too.

**How we checked it.** The following entry reports all 18 initial migrations were marked `[X]`, showing they were applied.

**Connects to.** Previous: [Step 21 — Understand local and remote main branches](02-git-and-github.md#step-21) · Next: [Step 23 — Update your private development journal](02-git-and-github.md#step-23)


---

<a id="step-23"></a>

### Step 23: Update your private development journal

**Why we are doing this.** After initializing the database, we wanted the private journal to record the milestone and how it had been verified.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this PowerShell command.*

```powershell
Add-Content -Path "C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

## Django Database Initialization — Initial Migrations

### Objective
Initialize Django's built-in database tables using SQLite.

### Commands executed
1. python manage.py showmigrations
2. python manage.py migrate
3. python manage.py showmigrations

### Results
- Initially, all 18 migrations were unapplied [ ].
- The migrate command completed successfully.
- Verification confirmed all 18 migrations applied [X].
- Applications: admin, auth, contenttypes, sessions.

### What I learned
- A migration defines a database structure change.
- migrate applies pending migrations to the database.
- showmigrations displays which migrations have been applied.
- [X] means applied; [ ] means pending.

### Errors
None.

### Revision question
What is the difference between makemigrations and migrate?
'@
```

**How it works.** `Add-Content` appends a written account of the migration result to the journal file. This changes documentation, not the database.

**How we checked it.** The next note states the new journal entry was appended and checked.

**Connects to.** Previous: [Step 22 — Apply database migrations](02-git-and-github.md#step-22) · Next: [Step 24 — Stage the updated journal](02-git-and-github.md#step-24)


---

<a id="step-24"></a>

### Step 24: Stage the updated journal

**Why we are doing this.** The migration milestone had been written to the journal but was not yet staged for version control.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** `git add` stages the updated Markdown file. The database itself is not committed to the journal.

**How we checked it.** The source confirms staging completed; the line-ending warning was harmless.

**Connects to.** Previous: [Step 23 — Update your private development journal](02-git-and-github.md#step-23) · Next: [Step 25 — Committing Development Journal Update](03-database-and-migrations.md#step-25)

---

**Chapter recap.** Understand staging, commits, remotes, branches, and pushes without mixing the two repositories.

**Practice explaining:** Explain the difference between git add, git commit, and git push.

