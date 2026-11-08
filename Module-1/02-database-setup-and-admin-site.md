# Module 1, Chapter 2: Database setup and admin site

## Step 68: Database Reset

#### Next: Remove the old SQLite database

Our Module 0 database contains Django's initial tables and the temporary superuser. We need a fresh database so Django can create `CustomUser` as the user model from the beginning.

In the VS Code PowerShell terminal, from the folder containing `manage.py`, run:

```powershell
Remove-Item .\db.sqlite3
```

**Expected result:** No output. The file `db.sqlite3` disappears from the project folder.

This permanently deletes the Module 0 superuser and other records in that database. It does **not** delete your Python code or the migration file we just generated.

Do not run `migrate` yet.

*Previous: Step 67 (Migration Preparation)  |  Next: Step 69 (Database File Locked)*

---

## Step 69: Database File Locked

Windows cannot delete `db.sqlite3` because another process is currently using it.

The most likely cause is that **Django's development server is still running**.

#### Do this first

1. In VS Code, check your other terminal tabs.
2. Find the terminal running `python manage.py runserver`.
3. Press **Ctrl + C** to stop the server.

Then retry:

```powershell
Remove-Item .\db.sqlite3
```

**Expected result:** No output, and `db.sqlite3` disappears.

If the same error appears, share it. We will identify the process holding the file.

*Previous: Step 68 (Database Reset)  |  Next: Step 70 (Create Database Tables)*

---

## Step 70: Create Database Tables

#### Next: Apply database migrations

Now that the old database has been deleted, run:

```powershell
python manage.py migrate
```

**Expected output:** Several migrations ending with `OK`, including:

```text
Applying accounts.0001_initial... OK
```

This command will:

- Create a fresh `db.sqlite3` file.
- Create our `accounts_customuser` table.
- Create Django's other required tables.

**Important:** No user accounts exist in this new database yet. We'll create the administrator next.

*Previous: Step 69 (Database File Locked)  |  Next: Step 71 (Create Administrator)*

---
