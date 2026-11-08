# MODULE 0 — Foundations

## Chapter 4: Server and admin account (Steps 29–36)

**Where we are.** With the basic database present, we checked the development-server environment and prepared an administrator account.

**What this chapter gives us.** Recognize the project root, the URL-view-template path, and the Django superuser workflow.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Trace a page request from a browser through urls.py and views.py to a template.

### Find a step

- [Step 29: Confirming Terminal Environment](#step-29)
- [Step 30: Trace the homepage request](#step-30)
- [Step 31: Documenting Superuser Setup](#step-31)
- [Step 32: Staging Development Journal Update](#step-32)
- [Step 33: Understand git -C and git add](#step-33)
- [Step 34: Staging Development Journal Update](#step-34)
- [Step 35: Committing Development Journal Update](#step-35)
- [Step 36: Pushing Development Journal to GitHub](#step-36)

---

<a id="step-29"></a>

### Step 29: Confirming Terminal Environment

**Why we are doing this.** We needed to confirm the terminal was running commands from the correct virtual environment and project folder before starting the server.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Run.*

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py createsuperuser
```

**How it works.** The current prompt and `python` executable help locate the active environment. Running Django commands in the wrong folder cannot find `manage.py`.

**How we checked it.** The source contains follow-up environment checks, but not a new project-code change here.

**Connects to.** Previous: [Step 28 — Understanding the Migration Workflow](03-database-and-migrations.md#step-28) · Next: [Step 30 — Trace the homepage request](04-server-and-admin-account.md#step-30)


---

<a id="step-30"></a>

### Step 30: Trace the homepage request

**Why we are doing this.** With Django configured, we wanted to understand the page served at the application's root URL.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```html
    <h1>Inventory &amp; Demand Intelligence</h1>
    <p>Module 0 setup is running.</p>
    <p><a href="/admin/">Open Django admin</a></p>
    
```

**How it works.** A URL pattern selects a view; the view chooses a template; the template produces the HTML page. This explains the connection among URL, view, and browser.

**How we checked it.** The notes discuss the homepage's observed output and initial checks. No separate business function was added.

**Connects to.** Previous: [Step 29 — Confirming Terminal Environment](04-server-and-admin-account.md#step-29) · Next: [Step 31 — Documenting Superuser Setup](04-server-and-admin-account.md#step-31)


---

<a id="step-31"></a>

### Step 31: Documenting Superuser Setup

**Why we are doing this.** A usable Django admin site requires an administrator account. We documented how one is created, not just the end result.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```powershell
Add-Content -Path "C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

## Django Admin Setup

### Objective
Create and verify an administrator account.

### Commands
- python manage.py createsuperuser
- python manage.py runserver

### Results
- Superuser created successfully.
- Django development server started without errors.
- Admin login succeeded at http://127.0.0.1:8000/admin/
- Django Administration dashboard loaded.

### What I learned
- A superuser has full Django administration permissions.
- Django Admin is provided by the framework.
- User credentials are stored in the database, with passwords stored as hashes.

### Errors
None.

### Revision question
What is the difference between a Django staff user and a superuser?
'@
```

**How it works.** `createsuperuser` asks for account details and saves a superuser with elevated permissions. Its password should never be written into the journal.

**How we checked it.** The recorded setup notes mention the administrator workflow, but do not reproduce the account's private credentials.

**Connects to.** Previous: [Step 30 — Trace the homepage request](04-server-and-admin-account.md#step-30) · Next: [Step 32 — Staging Development Journal Update](04-server-and-admin-account.md#step-32)


---

<a id="step-32"></a>

### Step 32: Staging Development Journal Update

**Why we are doing this.** After adding the administrator milestone to the journal, we prepared the updated file for a new commit.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 40 completed successfully, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** The `git add` command stages the current Markdown notes, keeping this documentation change separate from the Django application.

**How we checked it.** No independent project test belongs to this documentation-only step.

**Connects to.** Previous: [Step 31 — Documenting Superuser Setup](04-server-and-admin-account.md#step-31) · Next: [Step 33 — Understand git -C and git add](04-server-and-admin-account.md#step-33)


---

<a id="step-33"></a>

### Step 33: Understand git -C and git add

**Why we are doing this.** The Git syntax `git -C <folder> add ...` was unclear, so we explained the command before proceeding.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Your command.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** `-C` changes Git's working directory for that command; `add` stages the named file. Neither option uploads content.

**How we checked it.** This was an explanation of the command already used, not another application change.

**Connects to.** Previous: [Step 32 — Staging Development Journal Update](04-server-and-admin-account.md#step-32) · Next: [Step 34 — Staging Development Journal Update](04-server-and-admin-account.md#step-34)


---

<a id="step-34"></a>

### Step 34: Staging Development Journal Update

**Why we are doing this.** With the command understood, we staged the updated journal again.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming you've completed Step 40 (adding the Django Admin notes), run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** The source repeats the staging instruction so the user could continue confidently after clarifying `-C`.

**How we checked it.** No new functionality or independent test was recorded.

**Connects to.** Previous: [Step 33 — Understand git -C and git add](04-server-and-admin-account.md#step-33) · Next: [Step 35 — Committing Development Journal Update](04-server-and-admin-account.md#step-35)


---

<a id="step-35"></a>

### Step 35: Committing Development Journal Update

**Why we are doing this.** The staged server/admin notes needed a local history entry.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 41 completed successfully, run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django admin setup"
```

**How it works.** The commit message captures this documentation milestone; the commit belongs to the journal repository rather than the Django app.

**How we checked it.** The next step publishes the journal change.

**Connects to.** Previous: [Step 34 — Staging Development Journal Update](04-server-and-admin-account.md#step-34) · Next: [Step 36 — Pushing Development Journal to GitHub](04-server-and-admin-account.md#step-36)


---

<a id="step-36"></a>

### Step 36: Pushing Development Journal to GitHub

**Why we are doing this.** The latest journal commit was still only on the laptop until it was pushed.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 42 completed successfully, run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How it works.** `git push` sends the journal's tracked branch to its private GitHub remote.

**How we checked it.** The source includes follow-up confirmation and discussion of the remote state.

**Connects to.** Previous: [Step 35 — Committing Development Journal Update](04-server-and-admin-account.md#step-35) · Next: [Step 37 — Staging Security Verification Notes](05-security-checks.md#step-37)

---

**Chapter recap.** Recognize the project root, the URL-view-template path, and the Django superuser workflow.

**Practice explaining:** Trace a page request from a browser through urls.py and views.py to a template.

