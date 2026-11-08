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
