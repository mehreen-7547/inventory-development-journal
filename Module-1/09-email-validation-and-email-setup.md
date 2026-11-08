# MODULE 1 — Employee accounts

## Chapter 9: Email validation and email setup (Steps 108–112)

### Before we begin

Employee accounts were going to receive important links by email, so we needed better email addresses and a safe way to try sending messages.

### What we worked on

We required each account to have its own email, updated the database, and made Django print test emails in the terminal.

### Picture it this way

When we say “console email,” we mean Django prints the message where we ran the server. It does not send anything to an inbox yet. That temporary method is useful while building password recovery.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 108: Understand REQUIRED_FIELDS](#step-108)
- [Step 109: Enforce Email Validation](#step-109)
- [Step 110: Create Migration](#step-110)
- [Step 111: Apply Migration](#step-111)
- [Step 112: Email Configuration](#step-112)

---

<a id="step-108"></a>

### Step 108: Understand REQUIRED_FIELDS

**What was happening before this step?**

Django's administrator-creation command was asking for fields such as email and role. We wanted to know why.

**What did we do, and what does it mean?**

`REQUIRED_FIELDS` tells Django which extra fields to prompt for when creating a superuser from the terminal. It does not by itself decide all validation rules for every web form.

**File or place:** `accounts/models.py`.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```python
email = models.EmailField(unique=True, blank=False)
```

**How did we know what happened?**

The explanation distinguishes command-line prompts from model-level constraints.

**Where to go next:** Previous: [Step 107 — Test Role Security](08-role-security-decorators.md#step-107) · Next: [Step 109 — Enforce Email Validation](09-email-validation-and-email-setup.md#step-109)


---

<a id="step-109"></a>

### Step 109: Enforce Email Validation

**What was happening before this step?**

We were about to send password links by email, so we needed usable email addresses and no duplicates.

**What did we do, and what does it mean?**

If two employees share the same email, a reset message might reach the wrong person. We changed the user model so email is required and unique, then prepared to update the database to match.

**File or place:** `accounts/models.py`.

**Code or command from this step**

*1. Open accounts/models.py and update it to.*

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

*2. What did we add?.*

```python
email = models.EmailField(
    unique=True,
    blank=False
)
```

**Understanding the code, one piece at a time**

`blank=False` means an email is required during normal Django form validation. `unique=True` means each saved email value must be different from the others after the database has been updated. They protect different mistakes.

**How did we know what happened?**

The next steps create and apply the corresponding migration.

**Where to go next:** Previous: [Step 108 — Understand REQUIRED_FIELDS](09-email-validation-and-email-setup.md#step-108) · Next: [Step 110 — Create Migration](09-email-validation-and-email-setup.md#step-110)


---

<a id="step-110"></a>

### Step 110: Create Migration

**What was happening before this step?**

We changed the Python model, but the database table still had its old rules.

**What did we do, and what does it mean?**

`makemigrations` compares the model with the recorded schema and writes a migration file explaining the change. It prepares the database work; it does not apply it yet.

**File or place:** `accounts/migrations/0002_alter_customuser_email.py`.

**Code or command from this step**

*1. Open your VS Code terminal and run.*

```bash
python manage.py makemigrations accounts
```

*2. Example result from the notes (not a command to run).*

```text
Migrations for 'accounts':
  accounts/migrations/0002_alter_customuser_email.py
    ~ Alter field email on customuser
```

**Understanding the code, one piece at a time**

When Django creates a migration file, it stores the intended change to the table as code. That file goes into Git so other computers can apply the same change later.

**How did we know what happened?**

Our notes shows the expected `0002_alter_customuser_email.py` migration filename.

**Where to go next:** Previous: [Step 109 — Enforce Email Validation](09-email-validation-and-email-setup.md#step-109) · Next: [Step 111 — Apply Migration](09-email-validation-and-email-setup.md#step-111)


---

<a id="step-111"></a>

### Step 111: Apply Migration

**What was happening before this step?**

The migration instruction existed. Now the actual database needed to follow it.

**What did we do, and what does it mean?**

`migrate` applies the new rule so SQLite can reject duplicate email addresses. Until this command runs successfully, editing `models.py` alone is not enough.

**File or place:** `database (via migrate)`.

**Code or command from this step**

*1. Run.*

```bash
python manage.py migrate
```

*2. Example result from the notes (not a command to run).*

```text
Applying accounts.0002_alter_customuser_email... OK
```

**Understanding the code, one piece at a time**

The `migrate` command is the point where SQLite is asked to enforce the new email rule. Until it is applied, the Python model and the actual database may disagree.

**How did we know what happened?**

Our notes shows `Applying accounts.0002_alter_customuser_email... OK`.

**Where to go next:** Previous: [Step 110 — Create Migration](09-email-validation-and-email-setup.md#step-110) · Next: [Step 112 — Email Configuration](09-email-validation-and-email-setup.md#step-112)


---

<a id="step-112"></a>

### Step 112: Email Configuration

**What was happening before this step?**

We wanted to develop password-reset emails without sending real messages or exposing a Gmail password.

**What did we do, and what does it mean?**

The console email setting tells Django to print the email text and link in the VS Code terminal. This lets us test the message safely. We would switch to real sending only later.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Open config/settings.py and add these lines at the bottom.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**Understanding the code, one piece at a time**

The console backend is deliberately a fake delivery service for development. The application still prepares the email message and link, but instead of contacting Gmail, Django prints them in the terminal so we can inspect them.

**How did we know what happened?**

The notes explicitly report an email-uniqueness check had passed before configuring this backend.

**Where to go next:** Previous: [Step 111 — Apply Migration](09-email-validation-and-email-setup.md#step-111) · Next: [Step 113 — Forgot Password](10-forgot-and-reset-password.md#step-113)

---

### The point of this chapter

When we say “console email,” we mean Django prints the message where we ran the server. It does not send anything to an inbox yet. That temporary method is useful while building password recovery.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

