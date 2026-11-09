# MODULE 0 — Foundations

## Chapter 4: Server and admin account (Steps 29–36)

### Before we begin

We had the basic Django project, but needed to understand the homepage and prepare an account for Django's built-in administration screen.

### What we worked on

We looked at the path from a browser address to a Django function and an HTML page. We also saved how to create an administrator.

### Picture it this way

If someone opens the home address, Django needs to decide which view will answer. A view is a Python function that returns the page. An administrator account is a separate setup task.

### Find a step

- [Step 29: Confirming Terminal Environment](#step-29)
- [Step 30: Trace the homepage request](#step-30)
- [Step 31: Documenting Superuser Setup](#step-31)
- [Step 32: Staging Development Journal Update](#step-32)
- [Step 33: Understand git -C and git add](#step-33)
- [Step 34: Staging Development Journal Update](#step-34)
- [Step 35: Committing Development Journal Update](#step-35)

---

<a id="step-29"></a>

### Step 29: Confirming Terminal Environment

#### Before this step

Before starting the website, we checked that the terminal was in the correct project folder and was using the correct Python environment. The preceding step, Step 28, covered understanding the migration workflow, so this was the next part of the development sequence.

#### What we did and why

If the terminal points to the wrong folder, Django may not find manage.py. If it uses the wrong Python, it may not find installed packages. These checks prevent confusing errors before we even run the server.

#### Code and commands

**Step 29, block 1.** This code sample or terminal command was used while working on confirming terminal environment. The content below remains in its original sequence for Step 29, block 1.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py createsuperuser
```

**Step 29, block 2.** This code sample or terminal command was used while working on confirming terminal environment. The content below remains in its original sequence for Step 29, block 2.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**Step 29, block 3.** This code sample or terminal command was used while working on confirming terminal environment. The content below remains in its original sequence for Step 29, block 3.

```powershell
Start-Process "http://127.0.0.1:8000/admin/"
```

**Step 29, block 4.** This code sample or terminal command was used while working on confirming terminal environment. The content below remains in its original sequence for Step 29, block 4.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 29, confirming terminal environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py createsuperuser` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 29, confirming terminal environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver` starts Python or one of its tools from the development terminal.

**Block 3 (powershell, 1 lines).** In Step 29, confirming terminal environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Start-Process "http://127.0.0.1:8000/admin/"` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

**Block 4 (powershell, 1 lines).** In Step 29, confirming terminal environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver` starts Python or one of its tools from the development terminal.

#### How we checked it

For Step 29 (Confirming Terminal Environment), we compared the visible terminal output with the requested action. The visible result for Step 29 was described as “your output confirms”; this only addressed the confirming terminal environment check in `manage.py`.

#### Next

[Step 28: Understanding the Migration Workflow](../Module-0/03-database-and-migrations.md#step-28) · [Step 30: Trace the homepage request](#step-30)


---

<a id="step-30"></a>

### Step 30: Trace the homepage request

#### Before this step

With Django starting, we wanted to understand what happens when a person types the website's home address in a browser. Immediately before this, Step 29 dealt with confirming terminal environment; the work here continued the same part of the application.

#### What we did and why

Django first looks at its URL rules to decide which function should handle that address. That function is called a view. The view can then return an HTML page, called a template, for the browser to show.

#### Code and commands

**Step 30, block 1.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 1.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**Step 30, block 2.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 2.

```python
    path("", include("core.urls"))
    
```

**Step 30, block 3.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 3.

```python
    def home(request):
        return render(request, "core/home.html")
    
```

**Step 30, block 4.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 4.

```html
    <h1>Inventory &amp; Demand Intelligence</h1>
    <p>Module 0 setup is running.</p>
    <p><a href="/admin/">Open Django admin</a></p>
    
```

**Step 30, block 5.** This output was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 5.

```text
Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│
├── .venv/
│
└── techmindz_inventory_module0_sqlite_atomic/
    │
    ├── manage.py           ← HERE
    │
    ├── config/
    │   ├── settings.py
    │   ├── urls.py
    │   ├── asgi.py
    │   └── wsgi.py
    │
    └── core/
        ├── views.py
        ├── urls.py
        └── models.py
```

**Step 30, block 6.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 6.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**Step 30, block 7.** This code sample or terminal command was used while working on trace the homepage request. The content below remains in its original sequence for Step 30, block 7.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver` starts Python or one of its tools from the development terminal.

**Block 2 (python, 2 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `path("", include("core.urls"))` begins a URL rule connecting a browser address with the function that answers it.

**Block 3 (python, 3 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `def home(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `return render(request, "core/home.html")` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 4 (html, 4 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** The HTML element `<h1>Inventory &amp; Demand Intelligence</h1>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Module 0 setup is running.</p>` creates or closes part of the page structure seen by the browser. The link `<p><a href="/admin/">Open Django admin</a></p>` lets the user navigate to the named screen from this page.

**Block 5 (text, 18 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 30, output block 5 showed the result rather than another command. Its visible value was `Techmindz_Inventory_Module0_SQLite_Atomic_Update/`, which mattered for trace the homepage request.

**Block 6 (powershell, 1 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver` starts Python or one of its tools from the development terminal.

**Block 7 (powershell, 1 lines).** In Step 30, trace the homepage request involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 30. For Step 30, the proposed way to check trace the homepage request was to open the related page in the browser and check that the route reached the intended view or template in `manage.py`. Development continued in Step 31 before a complete result could be claimed.

#### Next

[Step 29: Confirming Terminal Environment](#step-29) · [Step 31: Documenting Superuser Setup](#step-31)


---

<a id="step-31"></a>

### Step 31: Documenting Superuser Setup

#### Before this step

The built-in Django administration page cannot be used until we have an account with permission to manage the site. Immediately before this, Step 30 dealt with trace the homepage request; the work here continued the same part of the application.

#### What we did and why

A superuser is a special Django account with broad access. Creating one gave us a way to open the administration page and check the project. It was a development setup step, not our finished employee-management screen.

#### Code and commands

**Step 31, block 1.** This code sample or terminal command was used while working on documenting superuser setup. The content below remains in its original sequence for Step 31, block 1.

```powershell
Add-Content -Path "C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

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

#### How the code works

**Block 1 (powershell, 28 lines).** In Step 31, documenting superuser setup involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The comment `## Django Admin Setup` explains the intention of the following section; it does not execute as application logic. The comment `### Objective` explains the intention of the following section; it does not execute as application logic.

**Lines 8–14:** The comment `### Commands` explains the intention of the following section; it does not execute as application logic. The command `- python manage.py createsuperuser` starts Python or one of its tools from the development terminal. The command `- python manage.py runserver` starts Python or one of its tools from the development terminal. The comment `### Results` explains the intention of the following section; it does not execute as application logic.

**Lines 15–21:** The comment `### What I learned` explains the intention of the following section; it does not execute as application logic.

**Lines 22–28:** The comment `### Errors` explains the intention of the following section; it does not execute as application logic. The comment `### Revision question` explains the intention of the following section; it does not execute as application logic.

#### How we checked it

Not tested yet at Step 31. For Step 31, the proposed way to check documenting superuser setup was to revisit `inventory-development-journal.md` and run the corresponding command or feature to see its result. Development continued in Step 32 before a complete result could be claimed.

#### Next

[Step 30: Trace the homepage request](#step-30) · [Step 32: Staging Development Journal Update](#step-32)


---

<a id="step-32"></a>

### Step 32: Staging Development Journal Update

#### Before this step

After documenting the administrator account, the new journal text needed to be selected for saving. Immediately before this, Step 31 dealt with documenting superuser setup; the work here continued the same part of the application.

#### What we did and why

Staging marks the journal update for the next commit. It does not create another administrator or change anyone's password.

#### Code and commands

**Step 32, block 1.** This code sample or terminal command was used while working on staging development journal update. The content below remains in its original sequence for Step 32, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 32, staging development journal update involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 32. For Step 32, the proposed way to check staging development journal update was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 33 before a complete result could be claimed.

#### Next

[Step 31: Documenting Superuser Setup](#step-31) · [Step 33: Understand git -C and git add](#step-33)


---

<a id="step-33"></a>

### Step 33: Understand git -C and git add

#### Before this step

The command included git -C, which looked unfamiliar, so we explained it before using it. Immediately before this, Step 32 dealt with staging development journal update; the work here continued the same part of the application.

#### What we did and why

-C tells Git to work inside a named folder without making us manually change folders first. The add part still means “put this file in the list for the next commit.”.

#### Code and commands

**Step 33, block 1.** This code sample or terminal command was used while working on understand git -c and git add. The content below remains in its original sequence for Step 33, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 33, understand git -c and git add involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 33. For Step 33, the proposed way to check understand git -c and git add was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 34 before a complete result could be claimed.

#### Next

[Step 32: Staging Development Journal Update](#step-32) · [Step 34: Staging Development Journal Update](#step-34)


---

<a id="step-34"></a>

### Step 34: Staging Development Journal Update

#### Before this step

We now understood the folder-targeting command and used it on the journal file. Immediately before this, Step 33 dealt with understand git -c and git add; the work here continued the same part of the application.

#### What we did and why

The action again selected the modified notes. Repetition here reflects the actual record of saving the journal after each milestone.

#### Code and commands

**Step 34, block 1.** This code sample or terminal command was used while working on staging development journal update. The content below remains in its original sequence for Step 34, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 34, staging development journal update involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 34. For Step 34, the proposed way to check staging development journal update was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 35 before a complete result could be claimed.

#### Next

[Step 33: Understand git -C and git add](#step-33) · [Step 35: Committing Development Journal Update](#step-35)


---

<a id="step-35"></a>

### Step 35: Committing Development Journal Update

#### Before this step

The journal's account-and-server notes were ready to be preserved. Immediately before this, Step 34 dealt with staging development journal update; the work here continued the same part of the application.

#### What we did and why

The commit stored that text as another local version. Committing a journal entry is different from running or testing Django itself.

#### Code and commands

**Step 35, block 1.** This code sample or terminal command was used while working on committing development journal update. The content below remains in its original sequence for Step 35, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django admin setup"
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 35, committing development journal update involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 35. For Step 35, the proposed way to check committing development journal update was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 36 before a complete result could be claimed.

#### Next

[Step 34: Staging Development Journal Update](#step-34) · [Step 36: Pushing Development Journal to GitHub](#step-36)
