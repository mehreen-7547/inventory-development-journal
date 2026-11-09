# MODULE 1 — Employee accounts

## Chapter 1: Accounts app and custom user model (Steps 56–67)

### Before we begin

The basic Django project could run, but it still treated users as generic Django accounts. Our business needs employees with different jobs.

### What we worked on

We created an accounts app and described a user record with roles for administrator, inventory manager and sales staff. We also learnt why Django already provides many username and password fields.

### Picture it this way

Every person signs in through the same Django login system, but their role helps us decide which pages to allow later. A model describes the employee entry; it does not become a database table until migrations are run.

### Find a step

- [Step 56: Begin the Accounts module](#step-56)
- [Step 57: Custom User Model](#step-57)
- [Step 58: Clarify account creation workflow](#step-58)
- [Step 59: App Creation](#step-59)
- [Step 60: Configure Settings](#step-60)
- [Step 61: Custom User Model](#step-61)
- [Step 62: Understand CustomUser inheritance](#step-62)
- [Step 63: Understanding AbstractUser](#step-63)

---

<a id="step-56"></a>

### Step 56: Begin the Accounts module

#### Before this step

Up to this point, we had mostly prepared Django and the development environment. Now we started the first feature ordinary users would interact with: accounts. The preceding step, Step 55, covered publish completion tag, so this was the next part of the development sequence.

#### What we did and why

The inventory system needed different people to sign in. We planned an administrator, an inventory manager, and sales staff. These roles would later decide who could open which pages.

#### Code and commands

**Step 56, block 1.** This code sample or terminal command was used while working on begin the accounts module. The content below remains in its original sequence for Step 56, block 1.

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

#### How the code works

**Block 1 (powershell, 2 lines).** In Step 56, begin the accounts module involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `python manage.py startapp accounts` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 56. For Step 56, the proposed way to check begin the accounts module was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 57 before a complete result could be claimed.

#### Next

[Step 55: Publish Completion Tag](../Module-0/07-module-0-completion.md#step-55) · [Step 57: Custom User Model](#step-57)


---

<a id="step-57"></a>

### Step 57: Custom User Model

#### Before this step

Django already knew how to store a username and password, but we needed to remember each person's role in our business. Immediately before this, Step 56 dealt with begin the accounts module; the work here continued the same part of the application.

#### What we did and why

We planned a custom user model: our own version of Django's user record, with an added role field. This lets one account represent a manager or a salesperson without creating a completely separate login system.

#### Code and commands

**Step 57, block 1.** This code sample or terminal command was used while working on custom user model. The content below remains in its original sequence for Step 57, block 1.

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

#### How the code works

**Block 1 (python, 18 lines).** In Step 57, custom user model involved `accounts/models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib.auth.models import AbstractUser` makes a Python function, class or module available to the code that follows. `from django.db import models` makes a Python function, class or module available to the code that follows. `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together. `ROLE_CHOICES = (` sets the named value used by the next operation in this file.

**Lines 8–14:** `role = models.CharField(` sets the named value used by the next operation in this file. `max_length=10,` sets the named value used by the next operation in this file. `choices=ROLE_CHOICES,` sets the named value used by the next operation in this file.

**Lines 15–18:** `default='sales'` sets the named value used by the next operation in this file. `REQUIRED_FIELDS = ['email', 'role']` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 57. For Step 57, the proposed way to check custom user model was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 58 before a complete result could be claimed.

#### Next

[Step 56: Begin the Accounts module](#step-56) · [Step 58: Clarify account creation workflow](#step-58)


---

<a id="step-58"></a>

### Step 58: Clarify account creation workflow

#### Before this step

Before creating database tables, we corrected how employee accounts were supposed to be added and used. Immediately before this, Step 57 dealt with custom user model; the work here continued the same part of the application.

#### What we did and why

This was a decision-checking step. We wanted the account rules clear first so we did not build a login process that contradicted our agreed workflow. It was not a second user model.

#### Code and commands

**Step 58, block 1.** This code sample or terminal command was used while working on clarify account creation workflow. The content below remains in its original sequence for Step 58, block 1.

```powershell
Set-Location "C:\Users\YOUR_WINDOWS_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"

python manage.py startapp accounts
```

**Step 58, block 2.** This output was used while working on clarify account creation workflow. The content below remains in its original sequence for Step 58, block 2.

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

#### How the code works

**Block 1 (powershell, 3 lines).** In Step 58, clarify account creation workflow involved `accounts/models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** The command `python manage.py startapp accounts` starts Python or one of its tools from the development terminal.

**Block 2 (text, 9 lines).** In Step 58, clarify account creation workflow involved `accounts/models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 58, output block 2 showed the result rather than another command. Its visible value was `accounts/`, which mattered for clarify account creation workflow.

#### How we checked it

Not tested yet at Step 58. For Step 58, the proposed way to check clarify account creation workflow was to revisit `accounts/models.py` and run the corresponding command or feature to see its result. Development continued in Step 59 before a complete result could be claimed.

#### Next

[Step 57: Custom User Model](#step-57) · [Step 59: App Creation](#step-59)


---

<a id="step-59"></a>

### Step 59: App Creation

#### Before this step

We needed a place inside Django to keep the files related to employees and login. Immediately before this, Step 58 dealt with clarify account creation workflow; the work here continued the same part of the application.

#### What we did and why

A Django app is a folder of related features. Creating the accounts app gave us a home for user models, forms, views, and URL rules. It did not automatically make login work.

#### Code and commands

**Step 59, block 1.** This code sample or terminal command was used while working on app creation. The content below remains in its original sequence for Step 59, block 1.

```powershell
cd "C:\Users\YOUR_WINDOWS_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"
python manage.py startapp accounts
```

#### How the code works

**Block 1 (powershell, 2 lines).** In Step 59, app creation involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `python manage.py startapp accounts` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 59. For Step 59, the proposed way to check app creation was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 60 before a complete result could be claimed.

#### Next

[Step 58: Clarify account creation workflow](#step-58) · [Step 60: Configure Settings](#step-60)


---

<a id="step-60"></a>

### Step 60: Configure Settings

#### Before this step

The new accounts folder existed, but Django would not automatically know that it should use it. Immediately before this, Step 59 dealt with app creation; the work here continued the same part of the application.

#### What we did and why

In the settings file, we registered the app and identified its user model. That tells Django to include its database definitions and use our employee account type for authentication.

#### Code and commands

**Step 60, block 1.** This code sample or terminal command was used while working on configure settings. The content below remains in its original sequence for Step 60, block 1.

```python
    "core",
    "accounts",
]
```

**Step 60, block 2.** This code sample or terminal command was used while working on configure settings. The content below remains in its original sequence for Step 60, block 2.

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 60, configure settings involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** The lines starting with `"core",` supply the values or page content for this part of config/settings.py. These lines are kept together because their order and indentation matter.

**Block 2 (python, 1 lines).** In Step 60, configure settings involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `AUTH_USER_MODEL = "accounts.CustomUser"` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 60. For Step 60, the proposed way to check configure settings was to revisit `config/settings.py` and run the corresponding command or feature to see its result. Development continued in Step 61 before a complete result could be claimed.

#### Next

[Step 59: App Creation](#step-59) · [Step 61: Custom User Model](#step-61)


---

<a id="step-61"></a>

### Step 61: Custom User Model

#### Before this step

With the app registered, it was time to define what an employee account actually contains. Immediately before this, Step 60 dealt with configure settings; the work here continued the same part of the application.

#### What we did and why

In accounts/models.py, the CustomUser class builds on Django's normal user fields and adds our role choice. A model is a Python description of information Django will store in a database table.

#### Code and commands

**Step 61, block 1.** This code sample or terminal command was used while working on custom user model. The content below remains in its original sequence for Step 61, block 1.

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

#### How the code works

**Block 1 (python, 18 lines).** In Step 61, custom user model involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib.auth.models import AbstractUser` makes a Python function, class or module available to the code that follows. `from django.db import models` makes a Python function, class or module available to the code that follows. `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together. `ROLE_CHOICES = (` sets the named value used by the next operation in this file.

**Lines 8–14:** `role = models.CharField(` sets the named value used by the next operation in this file. `max_length=10,` sets the named value used by the next operation in this file. `choices=ROLE_CHOICES,` sets the named value used by the next operation in this file.

**Lines 15–18:** `default='sales'` sets the named value used by the next operation in this file. `REQUIRED_FIELDS = ['email', 'role']` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 61. For Step 61, the proposed way to check custom user model was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 62 before a complete result could be claimed.

#### Next

[Step 60: Configure Settings](#step-60) · [Step 62: Understand CustomUser inheritance](#step-62)


---

<a id="step-62"></a>

### Step 62: Understand CustomUser inheritance

#### Before this step

The CustomUser class was short, but it seemed to gain many fields without writing them out. We needed to understand why. Immediately before this, Step 61 dealt with custom user model; the work here continued the same part of the application.

#### What we did and why

AbstractUser is Django's ready-made user blueprint. By inheriting from it, our class gets username, password and other account behaviour. We add only the business-specific difference: the role. Later migrations turn this model into database tables.

#### Code and commands

**Step 62, block 1.** This code sample or terminal command was used while working on understand customuser inheritance. The content below remains in its original sequence for Step 62, block 1.

```python
role = models.CharField(...)
```

**Step 62, block 2.** This code sample or terminal command was used while working on understand customuser inheritance. The content below remains in its original sequence for Step 62, block 2.

```python
class CustomUser(AbstractUser):
```

**Step 62, block 3.** This code sample or terminal command was used while working on understand customuser inheritance. The content below remains in its original sequence for Step 62, block 3.

```python
REQUIRED_FIELDS = ['email', 'role']
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 62, understand customuser inheritance involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `role = models.CharField(...)` sets the named value used by the next operation in this file.

**Block 2 (python, 1 lines).** In Step 62, understand customuser inheritance involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together.

**Block 3 (python, 1 lines).** In Step 62, understand customuser inheritance involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `REQUIRED_FIELDS = ['email', 'role']` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 62. For Step 62, the proposed way to check understand customuser inheritance was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 63 before a complete result could be claimed.

#### Next

[Step 61: Custom User Model](#step-61) · [Step 63: Understanding AbstractUser](#step-63)


---

<a id="step-63"></a>

### Step 63: Understanding AbstractUser

#### Before this step

We wanted to make sure that using AbstractUser did not create an unexplained second user table. Immediately before this, Step 62 dealt with understand customuser inheritance; the work here continued the same part of the application.

#### What we did and why

Think of it as borrowing a blueprint rather than copying a completed house. AbstractUser supplies definitions; CustomUser is the concrete model our project uses. Django creates tables for the concrete model after migrations.

#### Code and commands

**Step 63, block 1.** This output was used while working on understanding abstractuser. The content below remains in its original sequence for Step 63, block 1.

```text
.venv/
  Lib/
    site-packages/
      django/
        contrib/
          auth/
            models.py
```

**Step 63, block 2.** This code sample or terminal command was used while working on understanding abstractuser. The content below remains in its original sequence for Step 63, block 2.

```python
from django.contrib.auth.models import AbstractUser
```

**Step 63, block 3.** This code sample or terminal command was used while working on understanding abstractuser. The content below remains in its original sequence for Step 63, block 3.

```python
class Meta:
    abstract = True
```

**Step 63, block 4.** This code sample or terminal command was used while working on understanding abstractuser. The content below remains in its original sequence for Step 63, block 4.

```python
class CustomUser(AbstractUser):
    role = models.CharField(max_length=10)
```

#### How the code works

**Block 1 (text, 7 lines).** In Step 63, understanding abstractuser involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 63, output block 1 showed the result rather than another command. Its visible value was `.venv/`, which mattered for understanding abstractuser.

**Block 2 (python, 1 lines).** In Step 63, understanding abstractuser involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.contrib.auth.models import AbstractUser` makes a Python function, class or module available to the code that follows.

**Block 3 (python, 2 lines).** In Step 63, understanding abstractuser involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `class Meta:` declares a Python class, grouping related data or behaviour together. `abstract = True` sets the named value used by the next operation in this file.

**Block 4 (python, 2 lines).** In Step 63, understanding abstractuser involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together. `role = models.CharField(max_length=10)` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 63. For Step 63, the proposed way to check understanding abstractuser was to revisit `models.py` and run the corresponding command or feature to see its result. Development continued in Step 64 before a complete result could be claimed.

#### Next

[Step 62: Understand CustomUser inheritance](#step-62) · [Step 64: Inspect Django’s built-in User model](#step-64)
