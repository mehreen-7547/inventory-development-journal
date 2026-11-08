# MODULE 1 — Accounts

## Chapter 2: Database setup and admin site (Steps 68–72)

**Where we are.** Changing the user model required a fresh disposable development database and an administrator who could manage accounts.

**What this chapter gives us.** Apply the custom-user migration, create a superuser, and register the user model with Django Admin.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why db.sqlite3 was reset during early development and why that would be unsafe with real data.

### Find a step

- [Step 68: Database Reset](#step-68)
- [Step 69: Database File Locked](#step-69)
- [Step 70: Create Database Tables](#step-70)
- [Step 71: Create Administrator](#step-71)
- [Step 72: Register CustomUser](#step-72)

---

<a id="step-68"></a>

### Step 68: Database Reset

**Why we are doing this.** Module 0 had initialized SQLite with Django's original user model. Introducing a custom user model after that requires special care during an early disposable development setup.

**File / location.** `db.sqlite3 (historical disposable database)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In the VS Code PowerShell terminal, from the folder containing manage.py, run (historical; **do not rerun blindly**).*

```powershell
Remove-Item .\db.sqlite3
```

**How it works.** The historical command deletes the local `db.sqlite3` so tables can be recreated with `CustomUser` from the beginning. This is destructive and appropriate only for a disposable database with no data to preserve.

**How we checked it.** The following step reports the file was locked; do not repeat this command against a current database with real data.

**Important:** The database deletion shown here was for an early disposable SQLite database. Do not perform it on the current project or a database containing real data.

**Connects to.** Previous: [Step 67 — Migration Preparation](01-accounts-app-and-custom-user-model.md#step-67) · Next: [Step 69 — Database File Locked](02-database-setup-and-admin-site.md#step-69)


---

<a id="step-69"></a>

### Step 69: Database File Locked

**Why we are doing this.** Windows prevented the SQLite file from being removed because a running process still had it open.

**File / location.** `db.sqlite3`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet (historical; **do not rerun blindly**).*

```powershell
Remove-Item .\db.sqlite3
```

**How it works.** The notes explain stopping the Django development server before retrying the same file operation. A lock is an operating-system access issue, not a migration syntax error.

**How we checked it.** The next step proceeds after the old database was removed.

**Important:** The database deletion shown here was for an early disposable SQLite database. Do not perform it on the current project or a database containing real data.

**Connects to.** Previous: [Step 68 — Database Reset](02-database-setup-and-admin-site.md#step-68) · Next: [Step 70 — Create Database Tables](02-database-setup-and-admin-site.md#step-70)


---

<a id="step-70"></a>

### Step 70: Create Database Tables

**Why we are doing this.** After clearing the disposable database, we needed to create tables using the new custom-user migration plan.

**File / location.** `db.sqlite3`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Now that the old database has been deleted, run (historical; **do not rerun blindly**).*

```powershell
python manage.py migrate
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output: Several migrations ending with OK, including (historical; **do not rerun blindly**).*

```text
Applying accounts.0001_initial... OK
```

**How it works.** `migrate` executes the available migration files and creates fresh tables, including the concrete custom user table.

**Follow the flow.** When `migrate` runs, Django applies its built-in migrations and the accounts app's initial migration. The result is a SQLite database containing the tables the application needs, including the custom-user table. At this point the tables can store accounts, but no browser login page exists yet.

**How we checked it.** The source shows an expected `accounts.0001_initial ... OK` result; the administrator can now be created.

**Connects to.** Previous: [Step 69 — Database File Locked](02-database-setup-and-admin-site.md#step-69) · Next: [Step 71 — Create Administrator](02-database-setup-and-admin-site.md#step-71)


---

<a id="step-71"></a>

### Step 71: Create Administrator

**Why we are doing this.** With a user table in place, the system needed an administrator who could sign into Django Admin and manage accounts.

**File / location.** `terminal: createsuperuser`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
python manage.py createsuperuser
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output.*

```text
Superuser created successfully.
```

**How it works.** `createsuperuser` asks for user details and securely hashes the password before storing it. The terminal asks for configured required fields as well.

**How we checked it.** The source includes `Superuser created successfully` as the expected/observed message; no actual password is stored in these notes.

**Connects to.** Previous: [Step 70 — Create Database Tables](02-database-setup-and-admin-site.md#step-70) · Next: [Step 72 — Register CustomUser](02-database-setup-and-admin-site.md#step-72)


---

<a id="step-72"></a>

### Step 72: Register CustomUser

**Why we are doing this.** The custom user existed in the database, but Django Admin needed instructions on how to display and manage it.

**File / location.** `accounts/admin.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace its contents with.*

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

**How it works.** `UserAdmin` supplies Django's standard user management interface; extending its fields lets Admin show the new `role`. Registration connects `CustomUser` to that interface.

**Follow the flow.** `CustomUserAdmin(UserAdmin)` reuses Django's existing admin account interface. The `fieldsets` setting controls fields visible when editing an account; `add_fieldsets` controls the account-creation form. `admin.site.register(...)` tells Django to use this configuration when the administrator opens the user section.

**How we checked it.** The admin registration code was added; subsequent steps begin the application's own login pages.

**Connects to.** Previous: [Step 71 — Create Administrator](02-database-setup-and-admin-site.md#step-71) · Next: [Step 73 — Login View](03-login.md#step-73)

---

**Chapter recap.** Apply the custom-user migration, create a superuser, and register the user model with Django Admin.

**Practice explaining:** Explain why db.sqlite3 was reset during early development and why that would be unsafe with real data.

