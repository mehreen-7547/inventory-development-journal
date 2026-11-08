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

## Step 30: Understanding the Project Homepage

The page displaying your **project title and a link** is the project's homepage.

The command:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

doesn't create that page. It simply starts the server so Django can display pages defined in your project.

Django treats these as two different addresses:

- `http://127.0.0.1:8000/` — Your project's homepage.
- `http://127.0.0.1:8000/admin/` — Django's built-in administration interface.

#### How was that page created?

I checked your actual project code on GitHub. The homepage was already included in the initial project setup.

Three files work together:

    **1. `config/urls.py`**

    ```python
    path("", include("core.urls"))
    
```

    Sends homepage requests to the `core` application.

    **2. `core/views.py`**

    ```python
    def home(request):
        return render(request, "core/home.html")
    
```

    Tells Django which HTML page to display.

    **3. `core/templates/core/home.html`**

    ```html
    <h1>Inventory &amp; Demand Intelligence</h1>
    <p>Module 0 setup is running.</p>
    <p><a href="/admin/">Open Django admin</a></p>
    
```

    Defines the title, message, and clickable admin link.

**In short:** The URL configuration finds the view, the view loads the HTML template, and your browser displays it.

This is a temporary setup homepage, not the final inventory dashboard.

#### How we checked it

`manage.py` is located in the **Django project root**, outside both `config` and `core`.

Your folder structure:

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

**Why is it outside both folders?**

Because `manage.py` manages the entire Django project, not just one app.

For example, it lets you run the server, apply migrations, and create superusers.

That's why we've been running:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

We're pointing Python to `manage.py` inside the Django project folder.

#### How we checked it

#### Step 39 — Verify Django Admin Login

We previously created your superuser account. Now we'll verify that it works.

**1.** If the Django server isn't running, execute:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**2.** Open this address in your browser:

http://127.0.0.1:8000/admin/

**3.** Log in using the superuser credentials you created.

**Expected result:** The Django Administration dashboard should appear, showing **Users** and **Groups**.

Tell me whether you successfully logged in.

*Previous: Step 29 (Confirming Terminal Environment)  |  Next: Step 31 (Documenting Superuser Setup)*

---

## Step 31: Documenting Superuser Setup

**Step 39 completed.** Your superuser login works, and the Django Admin dashboard is accessible.

#### Step 40 — Record the successful setup

Open a **new VS Code terminal** so the Django server can continue running.

Execute:

```powershell
Add-Content -Path "C:\Users\mehre\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

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

**Important:** We document the setup, not your actual username, email, or password.

Tell me **done**. We'll save this journal update to GitHub before moving to the next development task.

*Previous: Step 30 (Understanding the Project Homepage)  |  Next: Step 32 (Staging Development Journal Update)*

---
