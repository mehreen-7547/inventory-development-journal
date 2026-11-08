# Module 1, Chapter 1: Accounts app and custom user model

## Step 56: Approved — Begin Development

Claude has **approved Module 1**, subject to the stated corrections. No further approval is needed.

#### Final role-permission matrix

| Action | Admin | Manager | Sales |
|---|:---:|:---:|:---:|
| Login, logout, change own password | Yes | Yes | Yes |
| View own dashboard | Yes | Yes | Yes |
| Access Django admin | Yes | No | No |
| Create, deactivate or reset user accounts | Yes | No | No |
| Change another user's role | Yes | No | No |
| Manage suppliers, including deactivate/reactivate | No | Yes | No |
| Manage products, including deactivate/reactivate | No | Yes | No |
| View active products and stock | No | Yes | Yes* |
| Record opening stock | No | Yes | No |
| Adjust stock and view movement history | No | Yes | No |
| Record sales | No | No | Yes |
| View own sales | No | N/A | Yes |
| View all sales | No | Yes | No |
| Void sales | No | Yes | No |
| Create and edit draft purchase orders | No | Yes | No |
| Order, cancel or close purchase orders | No | Yes | No |
| Edit expected date of an ordered PO | No | Yes | No |
| Record planned or unplanned receipts | No | Yes | No |
| View low-stock and overdue-order reports | No | Yes | No |
| Import historical sales CSV | No | Yes | No |
| Set forecasting service level | No | Yes | No |
| Generate forecasts and recommendations | No | Yes | No |
| Accept, defer or dismiss recommendations | No | Yes | No |
| Create draft PO from accepted recommendation | No | Yes | No |
| Mark anomalies Open / Reviewed / Expected | No | Yes | No |
| View business reports | No | Yes | No |
| Automatic ordering | Not available | Not available | Not available |

*Sales Staff can see only active products' SKU, name, price and available quantity.*

The Admin dashboard will show only a user count and a Django admin link. Sales Staff must ask a Manager to void sales. Every restricted URL will have server-side permission enforcement and tests.

The app structure remains the approved **10 core tables and 4 stretch tables**. `core` will contain only homepage/dashboard views, with no models or business logic.

#### Step 81 — Create the `accounts` app

In your VS Code PowerShell terminal, run:

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

**Expected result:** A new `accounts` folder appears with files such as `models.py`, `views.py` and `admin.py`.

This is the standard Django app-creation process used in your training.

Do not run migrations or delete the database yet. We'll configure the custom user model first and verify the existing data before the approved reset.

*Previous: Step 55 (Publish Completion Tag)  |  Next: Step 57 (Custom User Model)*

---

## Step 57: Custom User Model

#### Step 82 — Define our custom user model

Open **`accounts/models.py`** in VS Code and replace its contents with:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models


class CustomUser(AbstractUser):
    ROLE_CHOICES = (
        ('admin', 'Admin'),
        ('manager', 'Inventory Manager'),
        ('sales', 'Sales Staff'),
    )

    role = models.CharField(
        max_length=10,
        choices=ROLE_CHOICES,
        default='sales'
    )

    REQUIRED_FIELDS = ['email', 'role']
```

This follows the `CustomUser(AbstractUser)` approach from your instructor's `backend/thirdproject/doctors/models.py`.

The main difference is that our roles represent inventory-system users rather than doctors and patients.

    NEW vs CLASS — REQUIRED_FIELDS

  **Where:** `accounts/models.py` → `CustomUser`
  **In class:** `backend/thirdproject/doctors/models.py` defines a custom user and its roles, without `REQUIRED_FIELDS`.
  **In this project:** We add `REQUIRED_FIELDS = ['email', 'role']`.
  **How they differ:** Our `createsuperuser` command will ask for the user's role as well as email.
  **Why chosen:** Ensures we explicitly assign the Admin role when creating the administrator.
  **Other option:** Create the superuser first and assign its role afterward.

**Do not run migrations yet.** We must configure `AUTH_USER_MODEL` first.

*Previous: Step 56 (Approved — Begin Development)  |  Next: Step 58 (Workflow Correction)*

---

## Step 58: Workflow Correction

**You are correct. This was my mistake, not yours.**

I instructed you to create the `accounts` app in Step 81, but you never confirmed that you had executed the command. When you clicked **Next**, I incorrectly assumed the step was completed.

I should not have proceeded to editing `accounts/models.py` without verifying that the app existed.

#### What we have actually completed

- **Module 0:** Django project initialized, `core` app created, SQLite configured, and Django's 18 default migrations applied.
- **Custom inventory tables:** None created.
- **`accounts` app:** Not yet created, based on what you've told me.
- **Module 1:** Approved, but implementation has not yet been confirmed.

Your understanding of Module 0 is correct.

#### Corrected Step 81 — Create the accounts app

In your VS Code PowerShell terminal, run:

```powershell
Set-Location "C:\Users\mehre\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"

python manage.py startapp accounts
```

Django should automatically create:

```text
accounts/
    __init__.py
    admin.py
    apps.py
    models.py
    tests.py
    views.py
    migrations/
        __init__.py
```

**Stop here.** Do not edit `models.py`, change settings, or run migrations yet.

#### How we'll prevent this happening again

From now on, I will distinguish between **instructed**, **confirmed completed**, and **verified** steps. Clicking Next will not be treated as proof that a terminal command succeeded.

Once the `accounts` folder appears, confirm it. Only then will we proceed.

*Previous: Step 57 (Custom User Model)  |  Next: Step 59 (App Creation)*

---

## Step 59: App Creation

Understood. From now on, I'll use **response numbers only**, alongside Claude's module numbers.

You've run:

```powershell
cd "C:\Users\mehre\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"
python manage.py startapp accounts
```

**Please check VS Code Explorer:** You should now see an `accounts` folder containing `models.py`, `views.py`, `admin.py`, and other default Django files.

If the folder exists, confirm with **“created”**. If the command produced an error, share the error instead.

We won't modify any files until creation is confirmed.

*Previous: Step 58 (Workflow Correction)  |  Next: Step 60 (Configure Settings)*

---

## Step 60: Configure Settings

The `accounts` app is now created.

#### Configure the custom user model

Open **`config/settings.py`**.

**1.** Find `INSTALLED_APPS` and add `"accounts",` immediately after `"core",`:

```python
    "core",
    "accounts",
]
```

**2.** Immediately below the closing `]`, add:

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

This tells Django that our project will use the custom user model in the `accounts` app instead of Django's default User model.

Your instructor also used `AUTH_USER_MODEL` in the training project's settings, so this follows the classroom approach.

**Save the file.** Do not run migrations yet. We will define `CustomUser` next.

*Previous: Step 59 (App Creation)  |  Next: Step 61 (Custom User Model)*

---

## Step 61: Custom User Model

Assuming you've saved the two changes to `config/settings.py`, we can now define our user model.

#### Open `accounts/models.py`

Replace its default contents with:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models


class CustomUser(AbstractUser):
    ROLE_CHOICES = (
        ('admin', 'Admin'),
        ('manager', 'Inventory Manager'),
        ('sales', 'Sales Staff'),
    )

    role = models.CharField(
        max_length=10,
        choices=ROLE_CHOICES,
        default='sales'
    )

    REQUIRED_FIELDS = ['email', 'role']
```

This closely follows your instructor's `backend/thirdproject/doctors/models.py`, which also defines `CustomUser(AbstractUser)` with a `role` field.

  **NEW vs CLASS — REQUIRED_FIELDS**

  **Where:** `accounts/models.py` → `CustomUser`
  **In class:** `backend/thirdproject/doctors/models.py` defines a custom user without `REQUIRED_FIELDS`.
  **In this project:** We add `REQUIRED_FIELDS = ['email', 'role']`.
  **How they differ:** The `createsuperuser` command will explicitly ask for email and role.
  **Why chosen:** To assign the administrator's role during account creation.
  **Other option:** Assign the role manually after creating the superuser.

Save `models.py`. **Do not run migrations or delete the database yet.**

When saved, reply **done** so we can verify the configuration before proceeding.

*Previous: Step 60 (Configure Settings)  |  Next: Step 62 (Understanding CustomUser)*

---

## Step 62: Understanding CustomUser

**No. We will not use `createsuperuser` for every employee.** That command is only for creating an administrator with special privileges.

Let's understand what our code actually does.

#### 1. Is `CustomUser` a database table?

Yes. Django will create a table for it when we run migrations.

You noticed that our code defines only one field:

```python
role = models.CharField(...)
```

But look at this line:

```python
class CustomUser(AbstractUser):
```

**`AbstractUser` already provides several fields.** Our `CustomUser` inherits all of them and adds one more: `role`.

For example, the resulting table will contain:

| id | username | email | password | role |
|---|---|---|---|---|
| 1 | admin1 | admin@example.com | Hashed | admin |
| 2 | manager1 | manager@example.com | Hashed | manager |
| 3 | sales1 | sales@example.com | Hashed | sales |

Illustrative records only. Passwords are stored as secure hashes, not plain text. Other inherited fields include first name, last name, account status and permissions.

So we're **extending Django's existing user structure**, not defining an entire user table from scratch.

#### 2. Where does the email field come from?

It is inherited from `AbstractUser`.

This line:

```python
REQUIRED_FIELDS = ['email', 'role']
```

doesn't create those fields. It tells Django's `createsuperuser` command to ask for them.

One distinction: `REQUIRED_FIELDS` does not, by itself, make email mandatory for every account created through Django Admin.

#### 3. How will accounts be created?

      **Initial administrator**

      We run `python manage.py createsuperuser` once to create the first administrator.

      **All other employees**

      The administrator logs into Django Admin and creates their accounts, assigning either `manager` or `sales` as their role.

#### 4. What about the superuser you already created in Module 0?

That superuser belongs to Django's original `auth_user` table.

We are switching to our own `accounts_customuser` table.

Because Module 0 contains only development setup data, Claude approved a **one-time SQLite database reset**. Your old superuser will be deleted during that reset, and we'll create a new one with `role='admin'`.

We have **not performed that reset yet**.

**In short:** One custom user table holds all accounts. Each person has a username, password, email and role. The role determines which parts of the inventory system they can access.

No additional action is needed for this explanation. We can continue with `accounts/models.py` when you're ready.

*Previous: Step 61 (Custom User Model)  |  Next: Step 63 (Understanding AbstractUser)*

---

## Step 63: Understanding AbstractUser

You're almost correct. **`CustomUser` is a child of `AbstractUser`, but `AbstractUser` is not a database table.**

It is a Python class provided by Django that contains predefined user fields and functionality.

#### 1. Where is `AbstractUser`?

It is part of Django's installed source code, not something we created.

You can find it at approximately:

```text
.venv/
  Lib/
    site-packages/
      django/
        contrib/
          auth/
            models.py
```

Inside that file, Django defines a class called `AbstractUser`.

#### How to see the actual code

In VS Code, open `accounts/models.py`, then hold **Ctrl** and click `AbstractUser` in:

```python
from django.contrib.auth.models import AbstractUser
```

VS Code should take you directly to Django's source code, where you can inspect the class.

#### 2. What is an abstract model?

Think of it as a reusable blueprint.

    **AbstractUser**

    Python blueprint — no database table
    username · email · first_name · last_name · password and more

        **Django's User**
        Creates `auth_user`
        Used in Module 0

        **Our CustomUser**
        Creates `accounts_customuser`
        Adds the `role` field

Both classes inherit from `AbstractUser`, but only the concrete user model selected for the project is used for new user accounts.

#### 3. Why not just use `AbstractUser` directly?

Because it is marked as an **abstract model**:

```python
class Meta:
    abstract = True
```

This tells Django not to create a database table for `AbstractUser` itself.

When we write:

```python
class CustomUser(AbstractUser):
    role = models.CharField(max_length=10)
```

Django combines the inherited fields with our new `role` field and creates a real table when we run migrations.

**So the distinction is:**

- `AbstractUser` = reusable Python blueprint.
- `CustomUser` = our Python model based on that blueprint.
- `accounts_customuser` = the actual database table created from our model.

You can inspect `AbstractUser` in VS Code now. No database changes are needed.

*Previous: Step 62 (Understanding CustomUser)  |  Next: Step 64 (User Table Definition)*

---
