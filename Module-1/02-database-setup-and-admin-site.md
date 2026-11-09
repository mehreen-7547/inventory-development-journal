# MODULE 1 — Employee accounts

## Chapter 2: Database setup and admin site (Steps 68–72)

### Before we begin

We had written our new employee user model, but the old test database was prepared for Django's default user model.

### What we worked on

While the database still contained only disposable development data, we reset it, created tables for the new model, made an administrator and registered users in Django Admin.

### Picture it this way

This is an early-development correction. On a live system, deleting the database would lose real records, so a database reset is not a normal way to update employee models.

### Find a step

- [Step 68: Database Reset](#step-68)
- [Step 69: Database File Locked](#step-69)
- [Step 70: Create Database Tables](#step-70)
- [Step 71: Create Administrator](#step-71)
- [Step 72: Register CustomUser](#step-72)

---

<a id="step-68"></a>

### Step 68: Database Reset

#### Before this step

The starter database had already been created before our custom user model existed. That could leave early development tables referring to the wrong user type. The preceding step, Step 67, covered migration preparation, so this was the next part of the development sequence.

#### What we did and why

Because this was still a disposable test database, the written explanation show resetting it and rebuilding with the chosen user model. This is not something to repeat with real employee records: deleting a database also deletes its data.

#### Code and commands

**Step 68, block 1.** This code sample or terminal command was used while working on database reset. The content below remains in its original sequence for Step 68, block 1.

```powershell
Remove-Item .\db.sqlite3
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 68, database reset involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Remove-Item .\db.sqlite3` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 68. For Step 68, the proposed way to check database reset was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 69 before a complete result could be claimed.

#### Next

[Step 67: Migration Preparation](../Module-1/01-accounts-app-and-custom-user-model.md#step-67) · [Step 69: Database File Locked](#step-69)


---

<a id="step-69"></a>

### Step 69: Database File Locked

#### Before this step

Windows refused to delete the test database because another running program was still using the file. Immediately before this, Step 68 dealt with database reset; the work here continued the same part of the application.

#### What we did and why

A running Django server or database connection can keep SQLite open. We needed to stop the process holding the file before touching it. The lock error was a file-use problem, not a mistake in the CustomUser class.

#### Code and commands

**Step 69, block 1.** This code sample or terminal command was used while working on database file locked. The content below remains in its original sequence for Step 69, block 1.

```powershell
Remove-Item .\db.sqlite3
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 69, database file locked involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Remove-Item .\db.sqlite3` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 69. For Step 69, the proposed way to check database file locked was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 70 before a complete result could be claimed.

#### Next

[Step 68: Database Reset](#step-68) · [Step 70: Create Database Tables](#step-70)


---

<a id="step-70"></a>

### Step 70: Create Database Tables

#### Before this step

Once the disposable database was cleared, we recreated the tables using the updated Django models. Immediately before this, Step 69 dealt with database file locked; the work here continued the same part of the application.

#### What we did and why

The migration command followed the new instructions and built the user and other required tables. Only after this could we save real CustomUser records in that test database.

#### Code and commands

**Step 70, block 1.** This code sample or terminal command was used while working on create database tables. The content below remains in its original sequence for Step 70, block 1.

```powershell
python manage.py migrate
```

**Step 70, block 2.** This output was used while working on create database tables. The content below remains in its original sequence for Step 70, block 2.

```text
Applying accounts.0001_initial... OK
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 70, create database tables involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py migrate` starts Python or one of its tools from the development terminal.

**Block 2 (text, 1 lines).** In Step 70, create database tables involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 70, output block 2 showed the result rather than another command. Its visible value was `Applying accounts.0001_initial... OK`, which mattered for create database tables.

#### How we checked it

Not tested yet at Step 70. For Step 70, the proposed way to check create database tables was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 71 before a complete result could be claimed.

#### Next

[Step 69: Database File Locked](#step-69) · [Step 71: Create Administrator](#step-71)


---

<a id="step-71"></a>

### Step 71: Create Administrator

#### Before this step

The database could now store users, but nobody yet had an administrator login. Immediately before this, Step 70 dealt with create database tables; the work here continued the same part of the application.

#### What we did and why

createsuperuser asks for account details and creates a powerful account intended for setup and administration. This is different from the limited staff accounts we will add later.

#### Code and commands

**Step 71, block 1.** This code sample or terminal command was used while working on create administrator. The content below remains in its original sequence for Step 71, block 1.

```powershell
python manage.py createsuperuser
```

**Step 71, block 2.** This output was used while working on create administrator. The content below remains in its original sequence for Step 71, block 2.

```text
Superuser created successfully.
```

**Step 71, block 3.** This code sample or terminal command was used while working on create administrator. The content below remains in its original sequence for Step 71, block 3.

```powershell
python manage.py runserver
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 71, create administrator involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py createsuperuser` starts Python or one of its tools from the development terminal.

**Block 2 (text, 1 lines).** In Step 71, create administrator involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 71, output block 2 showed the result rather than another command. Its visible value was `Superuser created successfully.`, which mattered for create administrator.

**Block 3 (powershell, 1 lines).** In Step 71, create administrator involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py runserver` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 71. For Step 71, the proposed way to check create administrator was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 72 before a complete result could be claimed.

#### Next

[Step 70: Create Database Tables](#step-70) · [Step 72: Register CustomUser](#step-72)


---

<a id="step-72"></a>

### Step 72: Register CustomUser

#### Before this step

Our new user model existed, but the built-in Django admin page needed to be told how to show it. Immediately before this, Step 71 dealt with create administrator; the work here continued the same part of the application.

#### What we did and why

accounts/admin.py controls how records appear in Django Admin. Registering the model lets an administrator view and edit user records there; it does not change the database fields.

#### Code and commands

**Step 72, block 1.** This code sample or terminal command was used while working on register customuser. The content below remains in its original sequence for Step 72, block 1.

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

#### How the code works

**Block 1 (python, 16 lines).** In Step 72, register customuser involved `accounts/admin.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib import admin` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.admin import UserAdmin` makes a Python function, class or module available to the code that follows. `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `class CustomUserAdmin(UserAdmin):` declares a Python class, grouping related data or behaviour together.

**Lines 8–14:** `add_fieldsets = UserAdmin.add_fieldsets + (` sets the named value used by the next operation in this file.

**Lines 15–16:** The lines starting with `admin.site.register(CustomUser, CustomUserAdmin)` supply the values or page content for this part of accounts/admin.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 72. For Step 72, the proposed way to check register customuser was to revisit `accounts/admin.py` and run the corresponding command or feature to see its result. Step 72 did not establish a successful outcome for register customuser.

#### Next

[Step 71: Create Administrator](#step-71) · [Step 73: Login View](../Module-1/03-login.md#step-73)


### Check your understanding

Without looking at the code, explain why **database setup and admin site** was needed at this point, and describe what the application did differently after the final step of this chapter.
