# MODULE 1 — Employee accounts

## Chapter 9: Email validation and email setup (Steps 108–112)

### Before we begin

Employee accounts were going to receive important links by email, so we needed better email addresses and a safe way to try sending messages.

### What we worked on

We required each account to have its own email, updated the database, and made Django print test emails in the terminal.

### Picture it this way

When we say “console email,” we mean Django prints the message where we ran the server. It does not send anything to an inbox yet. That temporary method is useful while building password recovery.

### Find a step

- [Step 108: Understand REQUIRED_FIELDS](#step-108)
- [Step 109: Enforce Email Validation](#step-109)
- [Step 110: Create Migration](#step-110)
- [Step 111: Apply Migration](#step-111)
- [Step 112: Email Configuration](#step-112)

---

<a id="step-108"></a>

### Step 108: Understand REQUIRED_FIELDS

#### Before this step

Django's administrator-creation command was asking for fields such as email and role. We wanted to know why. The preceding step, Step 107, covered test role security, so this was the next part of the development sequence.

#### What we did and why

REQUIRED_FIELDS tells Django which extra fields to prompt for when creating a superuser from the terminal. It does not by itself decide all validation rules for every web form.

#### Code and commands

**Step 108, block 1.** This code sample or terminal command was used while working on understand required_fields. The content below remains in its original sequence for Step 108, block 1.

```bash
python manage.py createsuperuser
```

**Step 108, block 2.** This output was used while working on understand required_fields. The content below remains in its original sequence for Step 108, block 2.

```text
Username: admin
Email address: admin@example.com
Role: admin
Password:
Password (again):
```

**Step 108, block 3.** This code sample or terminal command was used while working on understand required_fields. The content below remains in its original sequence for Step 108, block 3.

```python
email = models.EmailField(unique=True, blank=False)
```

#### How the code works

**Block 1 (bash, 1 lines).** In Step 108, understand required_fields involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py createsuperuser` starts Python or one of its tools from the development terminal.

**Block 2 (text, 5 lines).** In Step 108, understand required_fields involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 108, output block 2 showed the result rather than another command. Its visible value was `Username: admin`, which mattered for understand required_fields.

**Block 3 (python, 1 lines).** In Step 108, understand required_fields involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `email = models.EmailField(unique=True, blank=False)` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 108. For Step 108, the proposed way to check understand required_fields was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 109 before a complete result could be claimed.

#### Next

[Step 107: Test Role Security](../Module-1/08-role-security-decorators.md#step-107) · [Step 109: Enforce Email Validation](#step-109)


---

<a id="step-109"></a>

### Step 109: Enforce Email Validation

#### Before this step

We were about to send password links by email, so we needed usable email addresses and no duplicates. Immediately before this, Step 108 dealt with understand required_fields; the work here continued the same part of the application.

#### What we did and why

If two employees share the same email, a reset message might reach the wrong person. We changed the user model so email is required and unique, then prepared to update the database to match.

#### Code and commands

**Step 109, block 1.** This code sample or terminal command was used while working on enforce email validation. The content below remains in its original sequence for Step 109, block 1.

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

**Step 109, block 2.** This code sample or terminal command was used while working on enforce email validation. The content below remains in its original sequence for Step 109, block 2.

```python
email = models.EmailField(
    unique=True,
    blank=False
)
```

#### How the code works

**Block 1 (python, 23 lines).** In Step 109, enforce email validation involved `accounts/models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib.auth.models import AbstractUser` makes a Python function, class or module available to the code that follows. `from django.db import models` makes a Python function, class or module available to the code that follows. `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together. `ROLE_CHOICES = (` sets the named value used by the next operation in this file.

**Lines 8–14:** `email = models.EmailField(` sets the named value used by the next operation in this file. `unique=True,` sets the named value used by the next operation in this file. `blank=False` sets the named value used by the next operation in this file.

**Lines 15–21:** `role = models.CharField(` sets the named value used by the next operation in this file. `max_length=10,` sets the named value used by the next operation in this file. `choices=ROLE_CHOICES,` sets the named value used by the next operation in this file. `default='sales'` sets the named value used by the next operation in this file.

**Lines 22–23:** `REQUIRED_FIELDS = ['email', 'role']` sets the named value used by the next operation in this file.

**Block 2 (python, 4 lines).** In Step 109, enforce email validation involved `accounts/models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** `email = models.EmailField(` sets the named value used by the next operation in this file. `unique=True,` sets the named value used by the next operation in this file. `blank=False` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 109. For Step 109, the proposed way to check enforce email validation was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 110 before a complete result could be claimed.

#### Next

[Step 108: Understand REQUIRED_FIELDS](#step-108) · [Step 110: Create Migration](#step-110)


---

<a id="step-110"></a>

### Step 110: Create Migration

#### Before this step

We changed the Python model, but the database table still had its old rules. Immediately before this, Step 109 dealt with enforce email validation; the work here continued the same part of the application.

#### What we did and why

makemigrations compares the model with the previous database structure and writes a migration file explaining the change. It prepares the database work; it does not apply it yet.

#### Code and commands

**Step 110, block 1.** This code sample or terminal command was used while working on create migration. The content below remains in its original sequence for Step 110, block 1.

```bash
python manage.py makemigrations accounts
```

**Step 110, block 2.** This output was used while working on create migration. The content below remains in its original sequence for Step 110, block 2.

```text
Migrations for 'accounts':
  accounts/migrations/0002_alter_customuser_email.py
    ~ Alter field email on customuser
```

#### How the code works

**Block 1 (bash, 1 lines).** In Step 110, create migration involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py makemigrations accounts` starts Python or one of its tools from the development terminal.

**Block 2 (text, 3 lines).** In Step 110, create migration involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 110, output block 2 showed the result rather than another command. Its visible value was `Migrations for 'accounts':`, which mattered for create migration.

#### How we checked it

Not tested yet at Step 110. For Step 110, the proposed way to check create migration was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 111 before a complete result could be claimed.

#### Next

[Step 109: Enforce Email Validation](#step-109) · [Step 111: Apply Migration](#step-111)


---

<a id="step-111"></a>

### Step 111: Apply Migration

#### Before this step

The migration instruction existed. Now the actual database needed to follow it. Immediately before this, Step 110 dealt with create migration; the work here continued the same part of the application.

#### What we did and why

migrate applies the new rule so SQLite can reject duplicate email addresses. Until this command runs successfully, editing models.py alone is not enough.

#### Code and commands

**Step 111, block 1.** This code sample or terminal command was used while working on apply migration. The content below remains in its original sequence for Step 111, block 1.

```bash
python manage.py migrate
```

**Step 111, block 2.** This output was used while working on apply migration. The content below remains in its original sequence for Step 111, block 2.

```text
Applying accounts.0002_alter_customuser_email... OK
```

#### How the code works

**Block 1 (bash, 1 lines).** In Step 111, apply migration involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py migrate` starts Python or one of its tools from the development terminal.

**Block 2 (text, 1 lines).** In Step 111, apply migration involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 111, output block 2 showed the result rather than another command. Its visible value was `Applying accounts.0002_alter_customuser_email... OK`, which mattered for apply migration.

#### How we checked it

Not tested yet at Step 111. For Step 111, the proposed way to check apply migration was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 112 before a complete result could be claimed.

#### Next

[Step 110: Create Migration](#step-110) · [Step 112: Email Configuration](#step-112)


---

<a id="step-112"></a>

### Step 112: Email Configuration

#### Before this step

We wanted to develop password-reset emails without sending real messages or exposing a Gmail password. Immediately before this, Step 111 dealt with apply migration; the work here continued the same part of the application.

#### What we did and why

The console email setting tells Django to print the email text and link in the VS Code terminal. This lets us test the message safely. We would switch to real sending only later.

#### Code and commands

**Step 112, block 1.** This code sample or terminal command was used while working on email configuration. The content below remains in its original sequence for Step 112, block 1.

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'

DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**Step 112, block 2.** This code sample or terminal command was used while working on email configuration. The content below remains in its original sequence for Step 112, block 2.

```bash
python manage.py shell
```

**Step 112, block 3.** This code sample or terminal command was used while working on email configuration. The content below remains in its original sequence for Step 112, block 3.

```powershell
cd techmindz_inventory_module0_sqlite_atomic
```

**Step 112, block 4.** This output was used while working on email configuration. The content below remains in its original sequence for Step 112, block 4.

```text
(.venv) PS C:\Users\YOUR_WINDOWS_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic>
```

**Step 112, block 5.** This code sample or terminal command was used while working on email configuration. The content below remains in its original sequence for Step 112, block 5.

```powershell
python manage.py shell
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 112, email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = 'no-reply@example.com'` sets the named value used by the next operation in this file.

**Block 2 (bash, 1 lines).** In Step 112, email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py shell` starts Python or one of its tools from the development terminal.

**Block 3 (powershell, 1 lines).** In Step 112, email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `cd techmindz_inventory_module0_sqlite_atomic` supply the values or page content for this part of config/settings.py. These lines are kept together because their order and indentation matter.

**Block 4 (text, 1 lines).** In Step 112, email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 112, output block 4 showed the result rather than another command. Its visible value was `(.venv) PS C:\Users\YOUR_WINDOWS_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomi`, which mattered for email configuration.

**Block 5 (powershell, 1 lines).** In Step 112, email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py shell` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 112. For Step 112, the proposed way to check email configuration was to start Django Shell, inspect the email settings and run a safe test send. Step 112 did not establish a successful outcome for email configuration.

#### Next

[Step 111: Apply Migration](#step-111) · [Step 113: Forgot Password](../Module-1/10-forgot-and-reset-password.md#step-113)


### Check your understanding

Without looking at the code, explain why **email validation and email setup** was needed at this point, and describe what the application did differently after the final step of this chapter.
