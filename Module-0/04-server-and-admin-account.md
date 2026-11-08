# MODULE 0 — Getting started

## Chapter 4: Server and admin account (Steps 29–36)

### Before we begin

We had the basic Django project, but needed to understand the homepage and prepare an account for Django's built-in administration screen.

### What we worked on

We looked at the path from a browser address to a Django function and an HTML page. We also recorded how to create an administrator.

### Picture it this way

If someone opens the home address, Django needs to decide which view will answer. A view is a Python function that returns the page. An administrator account is a separate setup task.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

Before starting the website, we checked that the terminal was in the correct project folder and was using the correct Python environment.

**What did we do, and what does it mean?**

If the terminal points to the wrong folder, Django may not find `manage.py`. If it uses the wrong Python, it may not find installed packages. These checks prevent confusing errors before we even run the server.

**Code or command from this step**

*1. Run.*

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py createsuperuser
```

**How did we know what happened?**

The notes contain follow-up environment checks, but not a new project-code change here.

**Where to go next:** Previous: [Step 28 — Understanding the Migration Workflow](03-database-and-migrations.md#step-28) · Next: [Step 30 — Trace the homepage request](04-server-and-admin-account.md#step-30)


---

<a id="step-30"></a>

### Step 30: Trace the homepage request

**What was happening before this step?**

With Django starting, we wanted to understand what happens when a person types the website's home address in a browser.

**What did we do, and what does it mean?**

Django first looks at its URL rules to decide which function should handle that address. That function is called a view. The view can then return an HTML page, called a template, for the browser to show.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```html
    <h1>Inventory &amp; Demand Intelligence</h1>
    <p>Module 0 setup is running.</p>
    <p><a href="/admin/">Open Django admin</a></p>
    
```

**Understanding the code, one piece at a time**

Look for `urlpatterns` in a URL file. Each `path(...)` connects a browser address to a view. The view may call `render(...)` to combine a template with data and send HTML back to the browser.

**How did we know what happened?**

The notes discuss the homepage's observed output and initial checks. No separate business function was added.

**Where to go next:** Previous: [Step 29 — Confirming Terminal Environment](04-server-and-admin-account.md#step-29) · Next: [Step 31 — Documenting Superuser Setup](04-server-and-admin-account.md#step-31)


---

<a id="step-31"></a>

### Step 31: Documenting Superuser Setup

**What was happening before this step?**

The built-in Django administration page cannot be used until we have an account with permission to manage the site.

**What did we do, and what does it mean?**

A superuser is a special Django account with broad access. Creating one gave us a way to open the administration page and check the project. It was a development setup step, not our finished employee-management screen.

**Code or command from this step**

*1. Code or command used at this point.*

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

**How did we know what happened?**

The recorded setup notes mention the administrator workflow, but do not reproduce the account's private credentials.

**Where to go next:** Previous: [Step 30 — Trace the homepage request](04-server-and-admin-account.md#step-30) · Next: [Step 32 — Staging Development Journal Update](04-server-and-admin-account.md#step-32)


---

<a id="step-32"></a>

### Step 32: Staging Development Journal Update

**What was happening before this step?**

After documenting the administrator account, the new journal text needed to be selected for saving.

**What did we do, and what does it mean?**

Staging marks the journal update for the next commit. It does not create another administrator or change anyone's password.

**Code or command from this step**

*1. Assuming Step 40 completed successfully, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

No independent project test belongs to this documentation-only step.

**Where to go next:** Previous: [Step 31 — Documenting Superuser Setup](04-server-and-admin-account.md#step-31) · Next: [Step 33 — Understand git -C and git add](04-server-and-admin-account.md#step-33)


---

<a id="step-33"></a>

### Step 33: Understand git -C and git add

**What was happening before this step?**

The command included `git -C`, which looked unfamiliar, so we explained it before using it.

**What did we do, and what does it mean?**

`-C` tells Git to work inside a named folder without making us manually change folders first. The `add` part still means “put this file in the list for the next commit.”

**Code or command from this step**

*1. Your command.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

This was an explanation of the command already used, not another application change.

**Where to go next:** Previous: [Step 32 — Staging Development Journal Update](04-server-and-admin-account.md#step-32) · Next: [Step 34 — Staging Development Journal Update](04-server-and-admin-account.md#step-34)


---

<a id="step-34"></a>

### Step 34: Staging Development Journal Update

**What was happening before this step?**

We now understood the folder-targeting command and used it on the journal file.

**What did we do, and what does it mean?**

The action again selected the modified notes. Repetition here reflects the actual record of saving the journal after each milestone.

**Code or command from this step**

*1. Assuming you've completed Step 40 (adding the Django Admin notes), run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

No new functionality or independent test was recorded.

**Where to go next:** Previous: [Step 33 — Understand git -C and git add](04-server-and-admin-account.md#step-33) · Next: [Step 35 — Committing Development Journal Update](04-server-and-admin-account.md#step-35)


---

<a id="step-35"></a>

### Step 35: Committing Development Journal Update

**What was happening before this step?**

The journal's account-and-server notes were ready to be preserved.

**What did we do, and what does it mean?**

The commit stored that text as another local version. Committing a journal entry is different from running or testing Django itself.

**Code or command from this step**

*1. Assuming Step 41 completed successfully, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django admin setup"
```

**How did we know what happened?**

The next step publishes the journal change.

**Where to go next:** Previous: [Step 34 — Staging Development Journal Update](04-server-and-admin-account.md#step-34) · Next: [Step 36 — Pushing Development Journal to GitHub](04-server-and-admin-account.md#step-36)


---

<a id="step-36"></a>

### Step 36: Pushing Development Journal to GitHub

**What was happening before this step?**

The newest journal version was only on the laptop, so we uploaded it.

**What did we do, and what does it mean?**

This push made the same saved notes available in the private GitHub journal. It did not change the application repository.

**Code or command from this step**

*1. Assuming Step 42 completed successfully, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How did we know what happened?**

Our notes includes follow-up confirmation and discussion of the remote state.

**Where to go next:** Previous: [Step 35 — Committing Development Journal Update](04-server-and-admin-account.md#step-35) · Next: [Step 37 — Staging Security Verification Notes](05-security-checks.md#step-37)

---

### The point of this chapter

If someone opens the home address, Django needs to decide which view will answer. A view is a Python function that returns the page. An administrator account is a separate setup task.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

