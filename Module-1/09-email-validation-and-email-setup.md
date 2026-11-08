# MODULE 1 — Accounts

## Chapter 9: Email validation and email setup (Steps 108–112)

**Where we are.** Welcome messages and password resets depend on reliable employee email addresses.

**What this chapter gives us.** Enforce email uniqueness, apply the schema migration, and configure a safe console email backend.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain the difference between form validation, a unique database constraint, and a migration.

### Find a step

- [Step 108: Understand REQUIRED_FIELDS](#step-108)
- [Step 109: Enforce Email Validation](#step-109)
- [Step 110: Create Migration](#step-110)
- [Step 111: Apply Migration](#step-111)
- [Step 112: Email Configuration](#step-112)

---

<a id="step-108"></a>

### Step 108: Understand REQUIRED_FIELDS

**Why we are doing this.** We needed to know why Django's `createsuperuser` command asked for email and role even though username and password are standard.

**File / location.** `accounts/models.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```python
email = models.EmailField(unique=True, blank=False)
```

**How it works.** `REQUIRED_FIELDS` lists extra fields to prompt for when creating a superuser. It does not by itself enforce uniqueness, database non-null rules, or form validation.

Django creates a database uniqueness constraint after the corresponding migration is applied.

**How we checked it.** The explanation distinguishes command-line prompts from model-level constraints.

**Connects to.** Previous: [Step 107 — Test Role Security](08-role-security-decorators.md#step-107) · Next: [Step 109 — Enforce Email Validation](09-email-validation-and-email-setup.md#step-109)


---

<a id="step-109"></a>

### Step 109: Enforce Email Validation

**Why we are doing this.** Employee email addresses would be used for reset and welcome links, so duplicate or blank values could send the wrong person a security link.

**File / location.** `accounts/models.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open accounts/models.py and update it to.*

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

*2. Recorded snippet; What did we add?.*

```python
email = models.EmailField(
    unique=True,
    blank=False
)
```

**How it works.** Changing the model field to `EmailField(unique=True, blank=False)` requires a non-empty email in ordinary model forms and prevents duplicates at the database level. Existing records must be corrected before migrating.

**Follow the flow.** `EmailField` validates an email-like value when processed through Django forms. `blank=False` makes the field required by normal model/form validation; `unique=True` also asks the database to reject duplicate values after migration. Before applying it, we must ensure existing rows have suitable distinct emails.

**How we checked it.** The next steps create and apply the corresponding migration.

**Connects to.** Previous: [Step 108 — Understand REQUIRED_FIELDS](09-email-validation-and-email-setup.md#step-108) · Next: [Step 110 — Create Migration](09-email-validation-and-email-setup.md#step-110)


---

<a id="step-110"></a>

### Step 110: Create Migration

**Why we are doing this.** The Python model changed in Step 109; SQLite did not automatically change its schema.

**File / location.** `accounts/migrations/0002_alter_customuser_email.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open your VS Code terminal and run.*

```bash
python manage.py makemigrations accounts
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output (approximately).*

```text
Migrations for 'accounts':
  accounts/migrations/0002_alter_customuser_email.py
    ~ Alter field email on customuser
```

**How it works.** `makemigrations accounts` compares model definitions to the current migration history and creates a migration file for the altered email field.

**How we checked it.** The source shows the expected `0002_alter_customuser_email.py` migration filename.

**Connects to.** Previous: [Step 109 — Enforce Email Validation](09-email-validation-and-email-setup.md#step-109) · Next: [Step 111 — Apply Migration](09-email-validation-and-email-setup.md#step-111)


---

<a id="step-111"></a>

### Step 111: Apply Migration

**Why we are doing this.** The new uniqueness rule would not be enforced in SQLite until its migration was applied.

**File / location.** `database (via migrate)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```bash
python manage.py migrate
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output (approximately).*

```text
Applying accounts.0002_alter_customuser_email... OK
```

**How it works.** `migrate` executes the field alteration against the database. Duplicate existing emails may cause this operation to fail, which is why accounts were checked first.

**Follow the flow.** The newly generated migration file is like an instruction sheet for changing the SQLite table. `migrate` runs it and records that it was applied. Without this step, changing `models.py` would not guarantee that the database actually enforces unique email addresses.

**How we checked it.** The source shows `Applying accounts.0002_alter_customuser_email... OK`.

**Connects to.** Previous: [Step 110 — Create Migration](09-email-validation-and-email-setup.md#step-110) · Next: [Step 112 — Email Configuration](09-email-validation-and-email-setup.md#step-112)


---

<a id="step-112"></a>

### Step 112: Email Configuration

**Why we are doing this.** Before sending real mail, we needed a safe way to test password emails without exposing credentials or messaging real employees.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open config/settings.py and add these lines at the bottom.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**How it works.** Django's console email backend prints messages in the terminal, allowing the developer to inspect links and message content. Real SMTP is introduced later.

**Follow the flow.** The console backend is a development substitute for external email delivery. `send_mail` still creates a message, but the backend prints it in the terminal. This is useful for inspecting reset links without paying for a service or sending accidental real messages.

**How we checked it.** The original notes explicitly report an email-uniqueness check had passed before configuring this backend.

**Connects to.** Previous: [Step 111 — Apply Migration](09-email-validation-and-email-setup.md#step-111) · Next: [Step 113 — Forgot Password](10-forgot-and-reset-password.md#step-113)

---

**Chapter recap.** Enforce email uniqueness, apply the schema migration, and configure a safe console email backend.

**Practice explaining:** Explain the difference between form validation, a unique database constraint, and a migration.

