# Module 1, Chapter 9: Email validation and email setup

## Step 108: REQUIRED_FIELDS

`REQUIRED_FIELDS = ['email', 'role']` tells Django **which additional information to ask for when creating a superuser through the terminal**.

For example, when you run:

```bash
python manage.py createsuperuser
```

Django prompts you for:

```text
Username: admin
Email address: admin@example.com
Role: admin
Password:
Password (again):
```

This happens because of `REQUIRED_FIELDS`.

#### Why didn't it work for `manager1` and `sales1`?

Because `REQUIRED_FIELDS` is **not a database validation rule**.

| Situation | Does `REQUIRED_FIELDS` enforce email? |
|---|---|
| Creating a superuser through `createsuperuser` | Yes, prompts for it |
| Creating a user through Django Admin | No |
| Creating a user through Python code | No |
| Saving a user to the database | No |

#### What we actually need

```python
email = models.EmailField(unique=True, blank=False)
```

- `EmailField` — validates email format through Django's forms and model validation.
- `unique=True` — prevents two users from having the same email.
- `blank=False` — requires an email in Django forms.

**In short:** `REQUIRED_FIELDS` controls the `createsuperuser` prompts. The email field definition controls validation and database uniqueness.

We'll keep `REQUIRED_FIELDS` and add the email field after correcting the existing accounts.

*Previous: Step 107 (Test Role Security)  |  Next: Step 109 (Enforce Email Validation)*

---

## Step 109: Enforce Email Validation

#### Next: Update the CustomUser model

Assuming you've assigned different email addresses to `manager1` and `sales1`, we can now enforce email validation.

Open **`accounts/models.py`** and update it to:

```python
from django.contrib.auth.models import AbstractUser
from django.db import models


class CustomUser(AbstractUser):
    ROLE_CHOICES = (
        ('admin', 'Admin'),
        ('manager', 'Inventory Manager'),
        ('sales', 'Sales Staff'),
    )

    email = models.EmailField(
        unique=True,
        blank=False
    )

    role = models.CharField(
        max_length=10,
        choices=ROLE_CHOICES,
        default='sales'
    )

    REQUIRED_FIELDS = ['email', 'role']
```

#### What did we add?

```python
email = models.EmailField(
    unique=True,
    blank=False
)
```

- **`EmailField`** — provides email-format validation in Django forms.
- **`unique=True`** — prevents duplicate email values in the database.
- **`blank=False`** — makes the email mandatory in Django forms, including Django Admin.

We retain `REQUIRED_FIELDS` because it serves a separate purpose during superuser creation.

**For now:** Save the file. Don't run migrations yet; we'll do that in the next step.

*Previous: Step 108 (REQUIRED_FIELDS)  |  Next: Step 110 (Create Migration)*

---

## Step 110: Create Migration

#### Next: Create the database migration

We've updated `CustomUser` to require unique email addresses. Now Django needs to prepare the corresponding database change.

Open your VS Code terminal and run:

```bash
python manage.py makemigrations accounts
```

**What happens?**

Django compares your updated `models.py` with the previous migration and creates a new migration file.

**Expected output (approximately):**

```text
Migrations for 'accounts':
  accounts/migrations/0002_alter_customuser_email.py
    ~ Alter field email on customuser
```

The migration filename may differ.

**Important:** This command creates the migration instructions. It does not apply them to the database yet.

Don't run `migrate` yet. We'll do that next.

*Previous: Step 109 (Enforce Email Validation)  |  Next: Step 111 (Apply Migration)*

---

## Step 111: Apply Migration

#### Next: Apply the email-field migration

We've created the migration file. Now we need to apply its changes to the actual database.

Before proceeding, ensure `admin`, `manager1`, and `sales1` have different, non-empty email addresses.

Run:

```bash
python manage.py migrate
```

**What does this do?**

Django executes any pending migrations, including our new email-field migration.

**Expected output (approximately):**

```text
Applying accounts.0002_alter_customuser_email... OK
```

After this, the database will enforce unique email addresses.

**Important:** If you receive an error, don't modify or delete anything. Send me the terminal output so we can identify the cause.

*Previous: Step 110 (Create Migration)  |  Next: Step 112 (Email Configuration)*

---

## Step 112: Email Configuration

**Email uniqueness test passed.**

Django correctly rejected `manager1@example.com` because another account already uses it.

You can leave the form without saving. The invalid email was not stored.

#### Next: Configure local email testing

Open **`config/settings.py`** and add these lines at the bottom:

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**What do these settings mean?**

- `EMAIL_BACKEND` tells Django how to handle outgoing emails.
- `console.EmailBackend` prints emails in the terminal instead of actually sending them.
- `DEFAULT_FROM_EMAIL` specifies the sender address.

  NEW vs CLASS — Email backend
  This introduces Django's built-in email configuration. It uses the same `VARIABLE = value` syntax as other settings in `settings.py`.

**For now:** Save the file. We'll test sending an email in the next step.

#### How we checked it

**No, you don't need to stop the server.**

Keep Django running in its current terminal.

1. Open a **new terminal** in VS Code using the `+` button in the Terminal panel.
2. Make sure your virtual environment `(.venv)` is active.
3. Run:

```bash
python manage.py shell
```

You can now test email sending while the Django server continues running in the first terminal.

#### How we checked it

You're currently **one folder above** the Django project directory.

First, run:

```powershell
cd techmindz_inventory_module0_sqlite_atomic
```

Your terminal should then show:

```text
(.venv) PS C:\Users\mehre\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic>
```

Now run:

```powershell
python manage.py shell
```

This opens Django's Python shell so you can test email sending.

**Keep your Django server running in the other terminal.**

*Previous: Step 111 (Apply Migration)  |  Next: Step 113 (Forgot Password)*

---
