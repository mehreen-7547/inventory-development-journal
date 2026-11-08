# Module 0, Chapter 3: Database and migrations

## Step 25: Committing Development Journal Update

**Step 33 completed.** The journal was staged successfully. The LF/CRLF warning is harmless.

#### Step 34 — Commit the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**What this does:** Creates a new local Git commit containing your migration notes.

It does not upload anything to GitHub yet.

Send me the output, and we'll push the commit to your private repository.

*Previous: Step 24 (Stage the updated journal)  |  Next: Step 26 (Committing Development Journal Update)*

---

## Step 26: Committing Development Journal Update

#### Step 34 — Commit the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**What this does:** Saves the staged migration notes as a new commit in your local private journal repository.

It does not upload anything to GitHub yet.

Send me the terminal output. Next, we'll push the commit to GitHub.

*Previous: Step 25 (Committing Development Journal Update)  |  Next: Step 27 (Pushing Development Journal to GitHub)*

---

## Step 27: Pushing Development Journal to GitHub

#### Step 35 — Push the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal push
```

**What this does:** Uploads your latest local journal commit to your private GitHub repository.

Because we previously configured the tracking branch, `git push` is sufficient.

**Expected result:** A message showing `main -> main`.

Send me the output. We'll verify the upload before continuing Django development.

#### How we checked it

**Step 35 completed.** I verified that your latest journal commit is available in your private GitHub repository.

#### Step 36 — Start the Django development server

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**What this does:**
- Starts Django's local development server.
- Checks whether the project can start successfully.
- Makes the application accessible through your browser.

**Expected output:**

```text
Starting development server at http://127.0.0.1:8000/
```

The terminal will remain occupied while the server is running. This is normal.

Send me the terminal output. We'll then test the application in your browser.

*Previous: Step 26 (Committing Development Journal Update)  |  Next: Step 28 (Understanding the Migration Workflow)*

---

## Step 28: Understanding the Migration Workflow

#### What exactly did we accomplish?

We prepared the database so Django can store and manage information such as user accounts, permissions, and login sessions.

Think of the database as an empty office building. Django provides the instructions for constructing the rooms (tables) it needs.

#### 1. Before initialization

      **Django project**
      Code and settings exist

      **Database**
      Tables not yet created

Your Django project was configured to use SQLite, but its built-in database structure had not yet been initialized.

#### 2. First command — `showmigrations`

```powershell
python manage.py showmigrations
```

This asks Django: **Which database changes have already been applied?**

Your output showed:

```text
auth
 [ ] 0001_initial
 [ ] 0002_alter_permission_name_max_length
```

`[ ]` meant the migration was pending.

A *migration* is a set of instructions describing a database structure change, such as creating a table or modifying a column.

#### 3. Second command — `migrate`

```powershell
python manage.py migrate
```

This told Django to execute all pending migrations.

    **Migration instructions**
    Provided by Django's installed apps

    migrate

    {#each [{name:"auth_user",desc:"User accounts"},{name:"auth_permission",desc:"Permissions"},{name:"django_session",desc:"Login sessions"},{name:"django_admin_log",desc:"Admin activity"}] as t}

          {t.name}
          {t.desc}

    {/each}

  Examples of tables created in the SQLite database

You saw 18 operations ending with `OK`. These are **18 migrations, not necessarily 18 tables**.

#### 4. Third command — `showmigrations` again

You repeated the first command and saw:

```text
auth
 [X] 0001_initial
 [X] 0002_alter_permission_name_max_length
```

`[X]` means Django successfully applied and recorded that migration.

Django maintains a table named `django_migrations` to remember which migrations have already run.

#### 5. What about `makemigrations`?

We did not run it because Django already supplies the migrations for its built-in applications.

| Command | Purpose |
|---|---|
| `makemigrations` | Generates migration instructions from model changes |
| `migrate` | Applies those instructions to the database |
| `showmigrations` | Displays migration status |

Later, when we create our own `Product` model, we'll use `makemigrations` followed by `migrate` to create its database table.

**Current result:** Django's built-in database structure is initialized. We have not yet created the inventory-specific tables.

---

#### Quick understanding check

{@body const [answer,setAnswer] = DIL.useState("")}

  **Suppose we create a Django `Product` model. Which command generates the migration instructions for it?**

    `showmigrations`
    `makemigrations`
    `migrate`

  {#if answer}

        **{answer==="b"?"Correct":"Not quite"}**

      `makemigrations` generates the instructions. `migrate` then applies them to the database.

  {/if}

#### How we checked it

#### Step 36 — Start the Django development server

Run this in your VS Code terminal:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**What this does:**
- Starts Django's local development server.
- Checks whether the project starts without errors.
- Makes the application accessible at `http://127.0.0.1:8000/`.

**Expected output:**

```text
Starting development server at http://127.0.0.1:8000/
```

The terminal will remain occupied while the server runs. This is normal.

**Next:** Send me the terminal output. We'll verify the application in your browser.

*Previous: Step 27 (Pushing Development Journal to GitHub)  |  Next: Step 29 (Confirming Terminal Environment)*

---
