# MODULE 0 — Getting started

## Chapter 2: Git and GitHub (Steps 7–24)

### Before we begin

The starter code was on the laptop, which meant we could lose track of changes or accidentally mix project files with study notes.

### What we worked on

We put the application code under Git, connected it to GitHub, and made a separate private repository for the learning journal.

### Picture it this way

A Git commit is a saved version on the laptop. A Git push sends that saved version to GitHub. We practise these actions repeatedly because they happen after nearly every development milestone.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

The starter project was on the laptop. We wanted a record of every change so an accidental edit would not destroy our work.

**What did we do, and what does it mean?**

Git is a tool that keeps a history of changes to files. `git init` starts that history inside the project folder; it does not upload anything to the internet.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic init -b main
```

**Understanding the code, one piece at a time**

`git init` does one thing: it starts Git history in the current folder. It does not mean the files are on GitHub. The later commands `git add`, `git commit` and `git push` each do their own job.

**How did we know what happened?**

The initial response indicated Git was not initialized; the next step proceeded to staging, consistent with initialization.

**Where to go next:** Previous: [Step 6 — Create .env](01-environment-setup.md#step-6) · Next: [Step 8 — Stage the project files](02-git-and-github.md#step-8)


---

<a id="step-8"></a>

### Step 8: Stage the project files

**What was happening before this step?**

Git had been switched on, but it had not been told which changed files to save in the next snapshot.

**What did we do, and what does it mean?**

`git add` puts chosen files into a temporary list for the next save. This list is called the staging area. Nothing has been saved as a commit or sent to GitHub yet.

**Code or command from this step**

*1. Run.*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add .
```

**Understanding the code, one piece at a time**

The dot in `git add .` means “the current folder and its contents.” Git will include files it is allowed to track, but `.gitignore` can keep private or temporary files out. It is worth checking what Git has staged before making a commit.

**How did we know what happened?**

The notes explicitly verified `.env.example` contained placeholders, but a final staged-file audit was not reproduced here.

**Where to go next:** Previous: [Step 7 — Initialize the Git repository](02-git-and-github.md#step-7) · Next: [Step 9 — Create our first Git commit](02-git-and-github.md#step-9)


---

<a id="step-9"></a>

### Step 9: Create our first Git commit

**What was happening before this step?**

The correct files were now selected. We needed to save the first meaningful version of the project.

**What did we do, and what does it mean?**

A commit is a named snapshot of the selected files. After this, Git can show what changed between this version and later ones. The commit stays on the laptop until we upload it.

**Code or command from this step**

*1. Example result from the notes (not a command to run).*

```text
System check identified no issues (0 silenced).
```

*2. Run.*

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "chore: initialize Django project (module 0)"
```

**Understanding the code, one piece at a time**

In `git commit -m "..."`, the words after `-m` are the message attached to the saved version. That message helps us recognise why we saved the project at that point.

**How did we know what happened?**

The notes record Django's system check returned zero configuration issues before the commit. That is not a complete application test.

**Where to go next:** Previous: [Step 8 — Stage the project files](02-git-and-github.md#step-8) · Next: [Step 10 — Connect your local project to GitHub](02-git-and-github.md#step-10)


---

<a id="step-10"></a>

### Step 10: Connect your local project to GitHub

**What was happening before this step?**

We had a saved project on the laptop, but no connection to the GitHub repository that would hold its online copy.

**What did we do, and what does it mean?**

A remote is simply a GitHub destination. Giving it the nickname `origin` lets later commands send our commits there. Connecting a remote does not upload files by itself.

**Code or command from this step**

*1. Run this in your existing VS Code terminal. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**How did we know what happened?**

The notes refer to a public application repository. Connection and pushing were addressed in subsequent steps.

**Where to go next:** Previous: [Step 9 — Create our first Git commit](02-git-and-github.md#step-9) · Next: [Step 11 — Connect your local project to GitHub](02-git-and-github.md#step-11)


---

<a id="step-11"></a>

### Step 11: Connect your local project to GitHub

**What was happening before this step?**

Because we also had a separate learning journal, we checked that the project folder was connected to the correct GitHub destination.

**What did we do, and what does it mean?**

The same Git command can point two folders to different repositories. This check was about avoiding a mix-up: application code belongs in the application repository, and private notes belong in the journal repository.

**Code or command from this step**

*1. In your existing VS Code terminal, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**How did we know what happened?**

The later note says the remote connection was verified. This historical repeat should not be executed twice.

**Where to go next:** Previous: [Step 10 — Connect your local project to GitHub](02-git-and-github.md#step-10) · Next: [Step 12 — Push your first commit to GitHub](02-git-and-github.md#step-12)


---

<a id="step-12"></a>

### Step 12: Push your first commit to GitHub

**What was happening before this step?**

Our first project commit existed only on the laptop. We wanted a copy on GitHub as well.

**What did we do, and what does it mean?**

`git push` sends local commits to the connected GitHub repository. This is different from committing: commit saves a version locally; push uploads it.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push -u origin main
```

**How did we know what happened?**

The next note confirms the public application repository and separate private journal repository existed.

**Where to go next:** Previous: [Step 11 — Connect your local project to GitHub](02-git-and-github.md#step-11) · Next: [Step 13 — Create a separate local journal folder](02-git-and-github.md#step-13)


---

<a id="step-13"></a>

### Step 13: Create a separate local journal folder

**What was happening before this step?**

We wanted to keep detailed study notes without mixing long explanations into the Django application's code folders.

**What did we do, and what does it mean?**

We created a separate folder for the learning journal. Think of it as a notebook beside the project, not part of the running website. Its history and GitHub destination can be managed independently.

**Code or command from this step**

*1. In your existing VS Code PowerShell terminal, run.*

```powershell
New-Item -ItemType Directory -Path C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal
```

*2. Example result from the notes (not a command to run).*

```text
projects2026/
├── Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│   └── techmindz_inventory_module0_sqlite_atomic/
│       └── Django project (public Git repository)
│
└── inventory-development-journal/
    └── Learning journal (private Git repository)
```

**How did we know what happened?**

The next step says the private journal directory had been created successfully.

**Where to go next:** Previous: [Step 12 — Push your first commit to GitHub](02-git-and-github.md#step-12) · Next: [Step 14 — Initialize Git for the private journal](02-git-and-github.md#step-14)


---

<a id="step-14"></a>

### Step 14: Initialize Git for the private journal

**What was happening before this step?**

The journal folder existed, but it did not yet have its own change history.

**What did we do, and what does it mean?**

Running Git initialization inside the journal starts a second, independent history. Saving the journal does not automatically save or change the Django application.

**Code or command from this step**

*1. Run this in your existing VS Code terminal. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal init -b main
```

**How did we know what happened?**

Our notes confirms the private journal was initialized on `main`.

**Where to go next:** Previous: [Step 13 — Create a separate local journal folder](02-git-and-github.md#step-13) · Next: [Step 15 — Connect it to GitHub](02-git-and-github.md#step-15)


---

<a id="step-15"></a>

### Step 15: Connect it to GitHub

**What was happening before this step?**

The private journal was ready locally. We needed to connect it to the right private GitHub repository.

**What did we do, and what does it mean?**

This command tells the journal folder where its online copy lives. A private repository is intended to restrict who can see the notes, unlike a publicly visible project repository.

**Code or command from this step**

*1. Run this in your existing terminal. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal remote add origin https://github.com/mehreen-7547/inventory-development-journal.git
```

**How did we know what happened?**

A later check confirmed the journal remote, although this step was partially repeated during setup.

**Where to go next:** Previous: [Step 14 — Initialize Git for the private journal](02-git-and-github.md#step-14) · Next: [Step 16 — Stage the journal for Git](02-git-and-github.md#step-16)


---

<a id="step-16"></a>

### Step 16: Stage the journal for Git

**What was happening before this step?**

The first journal file had been written, but Git was not yet preparing to save it.

**What did we do, and what does it mean?**

`git add` selects that one journal file for the next commit. This is useful because the journal could eventually contain many files and we may not want to commit all of them at once.

**Code or command from this step**

*1. Run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

The notes show a subsequent repetition of staging, so this entry describes the intended action rather than a distinct feature.

**Where to go next:** Previous: [Step 15 — Connect it to GitHub](02-git-and-github.md#step-15) · Next: [Step 17 — Understand staging versus committing](02-git-and-github.md#step-17)


---

<a id="step-17"></a>

### Step 17: Understand staging versus committing

**What was happening before this step?**

While learning Git, we needed an example that made “edit a file” different from “save an edit in Git.”

**What did we do, and what does it mean?**

Changing `views.py` changes the working file, but Git does not create a snapshot automatically. You must stage and commit. The code discussed here was used to explain that difference; it was not the start of a new inventory feature.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```bash
git commit -m "Add inventory views"
```

**How did we know what happened?**

No Django view was created in this step. The note was explanatory only.

**Where to go next:** Previous: [Step 16 — Stage the journal for Git](02-git-and-github.md#step-16) · Next: [Step 18 — Stage the private journal](02-git-and-github.md#step-18)


---

<a id="step-18"></a>

### Step 18: Stage the private journal

**What was happening before this step?**

After that explanation, we returned to our actual journal folder and selected the journal file for saving.

**What did we do, and what does it mean?**

This repeated staging step mattered because we were working with two separate Git repositories. It made sure the journal, not the application code, was the file being prepared.

**Code or command from this step**

*1. Run this in your VS Code terminal.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

The following step shows the intended local commit operation.

**Where to go next:** Previous: [Step 17 — Understand staging versus committing](02-git-and-github.md#step-17) · Next: [Step 19 — Commit the private development journal](02-git-and-github.md#step-19)


---

<a id="step-19"></a>

### Step 19: Commit the private development journal

**What was happening before this step?**

The journal file was selected. We could now save its first version in the journal's own history.

**What did we do, and what does it mean?**

The commit records what the journal said at that moment. If the notes change later, we will still be able to see this earlier version.

**Code or command from this step**

*1. Assuming Step 24 showed only inventory-development-journal.md, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: add initial development and setup journal"
```

**How did we know what happened?**

The record then proceeds to publishing the journal, indicating the local commit workflow was followed.

**Where to go next:** Previous: [Step 18 — Stage the private journal](02-git-and-github.md#step-18) · Next: [Step 20 — Push your journal to private GitHub](02-git-and-github.md#step-20)


---

<a id="step-20"></a>

### Step 20: Push your journal to private GitHub

**What was happening before this step?**

The journal commit was safe locally but still absent from its GitHub repository.

**What did we do, and what does it mean?**

Pushing copies the saved journal version to its online destination. It does not publish the Django application or merge the two repositories.

**Code or command from this step**

*1. Assuming the remote URL from Step 26 was correct, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push -u origin main
```

**How did we know what happened?**

Later source notes refer to the private repository as successfully synchronized.

**Where to go next:** Previous: [Step 19 — Commit the private development journal](02-git-and-github.md#step-19) · Next: [Step 21 — Understand local and remote main branches](02-git-and-github.md#step-21)


---

<a id="step-21"></a>

### Step 21: Understand local and remote main branches

**What was happening before this step?**

Both the application and journal used a branch named `main`, which could make it seem as though they were connected.

**What did we do, and what does it mean?**

A branch is one line of history inside one repository. Two different repositories can each have a `main` branch without sharing files. A local `main` is your working version; `origin/main` describes the version known on GitHub.

**Code or command from this step**

*1. In our command.*

```powershell
git push -u origin main
```

**How did we know what happened?**

This was a conceptual explanation. No additional file change or standalone test occurred.

**Where to go next:** Previous: [Step 20 — Push your journal to private GitHub](02-git-and-github.md#step-20) · Next: [Step 22 — Apply database migrations](02-git-and-github.md#step-22)


---

<a id="step-22"></a>

### Step 22: Apply database migrations

**What was happening before this step?**

Django had been installed, but the database still lacked the tables Django needs for users, sessions, and the administration site.

**What did we do, and what does it mean?**

A database table is a place where Django stores a type of information. The migration command creates or changes tables according to Django's prepared instructions. We needed these basic tables before user accounts could work.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py migrate
```

**Understanding the code, one piece at a time**

`python manage.py migrate` asks Django to look for unfinished database changes and apply them. It works from migration files: it is not a command to copy Python code directly into the database.

**How did we know what happened?**

The following entry reports all 18 initial migrations were marked `[X]`, showing they were applied.

**Where to go next:** Previous: [Step 21 — Understand local and remote main branches](02-git-and-github.md#step-21) · Next: [Step 23 — Update your private development journal](02-git-and-github.md#step-23)


---

<a id="step-23"></a>

### Step 23: Update your private development journal

**What was happening before this step?**

We had created the starting database tables. We wanted the learning notes to record that important point.

**What did we do, and what does it mean?**

This did not change Django code. It updated the journal so that a future reader could see when the database was initialized and how the result was checked.

**Code or command from this step**

*1. Run this PowerShell command.*

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

**Understanding the code, one piece at a time**

`makemigrations` writes instructions about the database change; it does not change the tables yet. `migrate` is the separate command that applies those instructions.

**How did we know what happened?**

The next note states the new journal entry was appended and checked.

**Where to go next:** Previous: [Step 22 — Apply database migrations](02-git-and-github.md#step-22) · Next: [Step 24 — Stage the updated journal](02-git-and-github.md#step-24)


---

<a id="step-24"></a>

### Step 24: Stage the updated journal

**What was happening before this step?**

The new database notes had been written in the journal file but were not yet included in the next Git snapshot.

**What did we do, and what does it mean?**

Staging selects the updated notes. We followed the same add–commit–push process each time so the journal could be read from GitHub.

**Code or command from this step**

*1. Run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

Our notes confirms staging completed; the line-ending warning was harmless.

**Where to go next:** Previous: [Step 23 — Update your private development journal](02-git-and-github.md#step-23) · Next: [Step 25 — Committing Development Journal Update](03-database-and-migrations.md#step-25)

---

### The point of this chapter

A Git commit is a saved version on the laptop. A Git push sends that saved version to GitHub. We practise these actions repeatedly because they happen after nearly every development milestone.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

