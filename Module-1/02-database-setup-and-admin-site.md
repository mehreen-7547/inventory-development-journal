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

## Step 71: Create Administrator

#### Next: Create our project's administrator account

Run:

```powershell
python manage.py createsuperuser
```

When prompted, enter:

- **Username:** `admin`
- **Email:** Your email address
- **Role:** `admin`
- **Password:** A secure password of your choice
- **Password confirmation:** Enter the same password again

The exact prompt wording may vary. Your password will not be visible while typing.

**Expected output:**

```text
Superuser created successfully.
```

This creates a record in `accounts_customuser` with:

- `role = 'admin'` — our project's administrator role.
- `is_superuser = True` — Django's unrestricted superuser permission.
- `is_staff = True` — permission to access Django Admin.

We now have an administrator using our **CustomUser model**, rather than Django's original `User` model.

#### How we checked it

#### Next: Test the new administrator account

Start Django's development server:

```powershell
python manage.py runserver
```

Open Django Admin (http://127.0.0.1:8000/admin/) in your browser.

Log in using the **username and password you just created**.

**Expected result:** Django's administration dashboard opens successfully.

You may not see `CustomUser` listed yet. That's normal—we haven't registered our model in `accounts/admin.py`.

*Previous: Step 70 (Create Database Tables)  |  Next: Step 72 (Register CustomUser)*

---

## Step 72: Register CustomUser

#### Next: Update `accounts/admin.py`

Replace its contents with:

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

  NEW vs CLASS — CustomUserAdmin
  **Where:** `accounts/admin.py`

  **Class example:** `backend/thirdproject/doctors/admin.py` uses `admin.site.register(CustomUser)`.

  **Difference:** We extend Django's `UserAdmin` and add the `role` field to its forms.

  **Why:** This preserves Django's password-management functionality while allowing administrators to assign project roles.

  **Simpler alternative:** Register `CustomUser` directly, as in class, but that doesn't provide the same user-management forms.

**Expected result:** After saving, refresh `/admin/`. You should see **Custom users** under Accounts. Opening your administrator account should show an **Inventory Role** section.

*Previous: Step 71 (Create Administrator)  |  Next: Step 73 (Login View)*

---
