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
