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
