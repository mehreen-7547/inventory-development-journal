# MODULE 1 — Employee accounts

## Chapter 2: Database setup and admin site (Steps 68–72)

### Before we begin

We had written our new employee user model, but the old test database was prepared for Django's default user model.

### What we worked on

While the database still contained only disposable development data, we reset it, created tables for the new model, made an administrator and registered users in Django Admin.

### Picture it this way

This is an early-development correction. On a live system, deleting the database would lose real records, so a database reset is not a normal way to update employee models.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 68: Database Reset](#step-68)
- [Step 69: Database File Locked](#step-69)
- [Step 70: Create Database Tables](#step-70)
- [Step 71: Create Administrator](#step-71)
- [Step 72: Register CustomUser](#step-72)

---

<a id="step-68"></a>

### Step 68: Database Reset

**What was happening before this step?**

The starter database had already been created before our custom user model existed. That could leave early development tables referring to the wrong user type.

**What did we do, and what does it mean?**

Because this was still a disposable test database, the notes show resetting it and rebuilding with the chosen user model. This is not something to repeat with real employee records: deleting a database also deletes its data.

**File or place:** `db.sqlite3 (historical disposable database)`.

**Code or command from this step**

*1. In the VS Code PowerShell terminal, from the folder containing manage.py, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
Remove-Item .\db.sqlite3
```

**Understanding the code, one piece at a time**

These deletion commands belong to a very early throwaway database. They are dangerous once real employees or sales exist. In a working database, changes must be planned and migrated instead of deleting all records.

**How did we know what happened?**

The following step reports the file was locked; do not repeat this command against a current database with real data.

**Careful:** The database was disposable during these early steps. Deleting a real database would destroy saved employee data. Do not copy this reset procedure onto a working project.

**Where to go next:** Previous: [Step 67 — Migration Preparation](01-accounts-app-and-custom-user-model.md#step-67) · Next: [Step 69 — Database File Locked](02-database-setup-and-admin-site.md#step-69)


---

<a id="step-69"></a>

### Step 69: Database File Locked

**What was happening before this step?**

Windows refused to delete the test database because another running program was still using the file.

**What did we do, and what does it mean?**

A running Django server or database connection can keep SQLite open. We needed to stop the process holding the file before touching it. The lock error was a file-use problem, not a mistake in the `CustomUser` class.

**File or place:** `db.sqlite3`.

**Code or command from this step**

*1. Code or command used at this point. **Historical only: do not run it without understanding its effect on current files.***

```powershell
Remove-Item .\db.sqlite3
```

**How did we know what happened?**

The next step proceeds after the old database was removed.

**Careful:** The database was disposable during these early steps. Deleting a real database would destroy saved employee data. Do not copy this reset procedure onto a working project.

**Where to go next:** Previous: [Step 68 — Database Reset](02-database-setup-and-admin-site.md#step-68) · Next: [Step 70 — Create Database Tables](02-database-setup-and-admin-site.md#step-70)


---

<a id="step-70"></a>

### Step 70: Create Database Tables

**What was happening before this step?**

Once the disposable database was cleared, we recreated the tables using the updated Django models.

**What did we do, and what does it mean?**

The migration command followed the new instructions and built the user and other required tables. Only after this could we save real `CustomUser` records in that test database.

**File or place:** `db.sqlite3`.

**Code or command from this step**

*1. Now that the old database has been deleted, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
python manage.py migrate
```

*2. Example result from the notes (not a command to run). **Historical only: do not run it without understanding its effect on current files.***

```text
Applying accounts.0001_initial... OK
```

**Understanding the code, one piece at a time**

When the migration output says a migration finished successfully, it means Django followed those database instructions. You can now create the records the new tables are designed to hold.

**How did we know what happened?**

Our notes shows an expected `accounts.0001_initial ... OK` result; the administrator can now be created.

**Where to go next:** Previous: [Step 69 — Database File Locked](02-database-setup-and-admin-site.md#step-69) · Next: [Step 71 — Create Administrator](02-database-setup-and-admin-site.md#step-71)


---

<a id="step-71"></a>

### Step 71: Create Administrator

**What was happening before this step?**

The database could now store users, but nobody yet had an administrator login.

**What did we do, and what does it mean?**

`createsuperuser` asks for account details and creates a powerful account intended for setup and administration. This is different from the limited staff accounts we will add later.

**File or place:** `terminal: createsuperuser`.

**Code or command from this step**

*1. Run.*

```powershell
python manage.py createsuperuser
```

*2. Example result from the notes (not a command to run).*

```text
Superuser created successfully.
```

**How did we know what happened?**

Our notes includes `Superuser created successfully` as the expected/observed message; no actual password is stored in these notes.

**Where to go next:** Previous: [Step 70 — Create Database Tables](02-database-setup-and-admin-site.md#step-70) · Next: [Step 72 — Register CustomUser](02-database-setup-and-admin-site.md#step-72)


---

<a id="step-72"></a>

### Step 72: Register CustomUser

**What was happening before this step?**

Our new user model existed, but the built-in Django admin page needed to be told how to show it.

**What did we do, and what does it mean?**

`accounts/admin.py` controls how records appear in Django Admin. Registering the model lets an administrator view and edit user records there; it does not change the database fields.

**File or place:** `accounts/admin.py`.

**Code or command from this step**

*1. Replace its contents with.*

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import CustomUser


class CustomUserAdmin(UserAdmin):
    fieldsets = UserAdmin.fieldsets + (
        ('Inventory Role', {'fields': ('role',)}),
    )

    add_fieldsets = UserAdmin.add_fieldsets + (
        ('Inventory Role', {'fields': ('role',)}),
    )


admin.site.register(CustomUser, CustomUserAdmin)
```

**How did we know what happened?**

The admin registration code was added; subsequent steps begin the application's own login pages.

**Where to go next:** Previous: [Step 71 — Create Administrator](02-database-setup-and-admin-site.md#step-71) · Next: [Step 73 — Login View](03-login.md#step-73)

---

### The point of this chapter

This is an early-development correction. On a live system, deleting the database would lose real records, so a database reset is not a normal way to update employee models.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

