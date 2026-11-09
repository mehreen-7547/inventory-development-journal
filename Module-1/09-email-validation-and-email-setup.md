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
