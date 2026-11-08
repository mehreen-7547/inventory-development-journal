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
