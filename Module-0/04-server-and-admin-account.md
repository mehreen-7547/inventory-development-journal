# Module 0, Chapter 4: Server and admin account

## Step 29: Confirming Terminal Environment

**No problem. Your terminal is ready.**

Your output confirms:

- **Correct project directory:** Yes
- **Virtual environment activated:** Yes — `(.venv)`
- **Django server:** The previous terminal session has ended.

You haven't lost your project files, database, or Git commits.

#### Step 37 — Completed

We can proceed directly to the next step.

#### Step 38 — Create a Django superuser

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py createsuperuser
```

**What this does:** Starts the process of creating an administrator account for Django's admin panel.

Django will ask you for a username, email address, and password.

**Send me the first prompt you see.** We'll proceed one step at a time.

#### How we checked it

**Step 38 completed.** Your Django superuser account has been created successfully.

#### Step 39 — Start the Django server

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

Then open:

[http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)

Log in using the superuser credentials you just created.

**Expected result:** You should see the Django Administration dashboard with **Groups** and **Users**.

Tell me whether the dashboard opens successfully.

#### How we checked it

With the Django server running, open a **new PowerShell terminal** and execute:

```powershell
Start-Process "http://127.0.0.1:8000/admin/"
```

This opens the Django admin login page in your default browser.

#### How we checked it

Run the original command:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

Then open this address in your browser:

http://127.0.0.1:8000/admin/

*Previous: Step 28 (Understanding the Migration Workflow)  |  Next: Step 30 (Understanding the Project Homepage)*

---
