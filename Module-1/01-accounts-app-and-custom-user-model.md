# MODULE 1 — Employee accounts

## Chapter 1: Accounts app and custom user model (Steps 56–67)

### Before we begin

The basic Django project could run, but it still treated users as generic Django accounts. Our business needs employees with different jobs.

### What we worked on

We created an accounts app and described a user record with roles for administrator, inventory manager and sales staff. We also learnt why Django already provides many username and password fields.

### Picture it this way

Every person signs in through the same Django login system, but their role helps us decide which pages to allow later. A model describes the record; it does not become a database table until migrations are run.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 56: Begin the Accounts module](#step-56)
- [Step 57: Custom User Model](#step-57)
- [Step 58: Clarify account creation workflow](#step-58)
- [Step 59: App Creation](#step-59)
- [Step 60: Configure Settings](#step-60)
- [Step 61: Custom User Model](#step-61)
- [Step 62: Understand CustomUser inheritance](#step-62)
- [Step 63: Understanding AbstractUser](#step-63)
- [Step 64: Inspect Django’s built-in User model](#step-64)
- [Step 65: AUTH_USER_MODEL](#step-65)
- [Step 66: Django Authentication](#step-66)
- [Step 67: Migration Preparation](#step-67)

---

<a id="step-56"></a>

### Step 56: Begin the Accounts module

**What was happening before this step?**

Up to this point, we had mostly prepared Django and the development environment. Now we started the first feature ordinary users would interact with: accounts.

**What did we do, and what does it mean?**

The inventory system needed different people to sign in. We planned an administrator, an inventory manager, and sales staff. These roles would later decide who could open which pages.

**Code or command from this step**

*1. In your VS Code PowerShell terminal, run.*

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

**How did we know what happened?**

No code test was expected before the account models and views were created.

**Where to go next:** Previous: [Step 55 — Publish Completion Tag](../Module-0/07-module-0-completion.md#step-55) · Next: [Step 57 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-57)


---

<a id="step-57"></a>

### Step 57: Custom User Model

**What was happening before this step?**

Django already knew how to store a username and password, but we needed to remember each person's role in our business.

**What did we do, and what does it mean?**

We planned a custom user model: our own version of Django's user record, with an added role field. This lets one account represent a manager or a salesperson without creating a completely separate login system.

**File or place:** `accounts/models.py`.

**Code or command from this step**

*1. Open accounts/models.py in VS Code and replace its contents with.*

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

**Understanding the code, one piece at a time**

A role field might contain values such as `admin`, `manager` or `sales`. These are business roles. Django already stores usernames and password information, so we did not need a separate password system for every role.

**How did we know what happened?**

The model definition was proposed here; migrations and actual account tests come in later steps.

**Where to go next:** Previous: [Step 56 — Begin the Accounts module](01-accounts-app-and-custom-user-model.md#step-56) · Next: [Step 58 — Clarify account creation workflow](01-accounts-app-and-custom-user-model.md#step-58)


---

<a id="step-58"></a>

### Step 58: Clarify account creation workflow

**What was happening before this step?**

Before creating database tables, we corrected how employee accounts were supposed to be added and used.

**What did we do, and what does it mean?**

This was a decision-checking step. We wanted the account rules clear first so we did not build a login process that contradicted our agreed workflow. It was not a second user model.

**Code or command from this step**

*1. In your VS Code PowerShell terminal, run.*

```powershell
Set-Location "C:\Users\YOUR_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"

python manage.py startapp accounts
```

**How did we know what happened?**

This is a design correction in the source, not a separate runtime operation.

**Where to go next:** Previous: [Step 57 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-57) · Next: [Step 59 — App Creation](01-accounts-app-and-custom-user-model.md#step-59)


---

<a id="step-59"></a>

### Step 59: App Creation

**What was happening before this step?**

We needed a place inside Django to keep the files related to employees and login.

**What did we do, and what does it mean?**

A Django app is a folder of related features. Creating the `accounts` app gave us a home for user models, forms, views, and URL rules. It did not automatically make login work.

**File or place:** `accounts/ (Django app)`.

**Code or command from this step**

*1. You've run.*

```powershell
cd "C:\Users\YOUR_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"
python manage.py startapp accounts
```

**How did we know what happened?**

The following steps add settings and model code; no independent test was recorded here.

**Where to go next:** Previous: [Step 58 — Clarify account creation workflow](01-accounts-app-and-custom-user-model.md#step-58) · Next: [Step 60 — Configure Settings](01-accounts-app-and-custom-user-model.md#step-60)


---

<a id="step-60"></a>

### Step 60: Configure Settings

**What was happening before this step?**

The new `accounts` folder existed, but Django would not automatically know that it should use it.

**What did we do, and what does it mean?**

In the settings file, we registered the app and identified its user model. That tells Django to include its database definitions and use our employee account type for authentication.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Find INSTALLEDAPPS and add "accounts", immediately after "core",.*

```python
    "core",
    "accounts",
]
```

*2. Immediately below the closing ], add.*

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

**Understanding the code, one piece at a time**

`INSTALLED_APPS` is Django's list of app folders to load. `AUTH_USER_MODEL` tells it which user class represents the signed-in person. Both are settings, but they answer different questions.

**How did we know what happened?**

The next step defines the model itself. This setting should be checked before migration.

**Where to go next:** Previous: [Step 59 — App Creation](01-accounts-app-and-custom-user-model.md#step-59) · Next: [Step 61 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-61)


---

<a id="step-61"></a>

### Step 61: Custom User Model

**What was happening before this step?**

With the app registered, it was time to define what an employee account actually contains.

**What did we do, and what does it mean?**

In `accounts/models.py`, the `CustomUser` class builds on Django's normal user fields and adds our role choice. A model is a Python description of information Django will store in a database table.

**File or place:** `accounts/models.py`.

**Code or command from this step**

*1. Replace its default contents with.*

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

**Understanding the code, one piece at a time**

`class CustomUser(AbstractUser)` means we are starting with Django's ordinary user behaviour and adding our own fields. We did not write a password checker in this model; Django already supplies one.

**How did we know what happened?**

The notes record the code but not a database verification at this exact step.

**Where to go next:** Previous: [Step 60 — Configure Settings](01-accounts-app-and-custom-user-model.md#step-60) · Next: [Step 62 — Understand CustomUser inheritance](01-accounts-app-and-custom-user-model.md#step-62)


---

<a id="step-62"></a>

### Step 62: Understand CustomUser inheritance

**What was happening before this step?**

The `CustomUser` class was short, but it seemed to gain many fields without writing them out. We needed to understand why.

**What did we do, and what does it mean?**

`AbstractUser` is Django's ready-made user blueprint. By inheriting from it, our class gets username, password and other account behaviour. We add only the business-specific difference: the role. Later migrations turn this model into database tables.

**File or place:** `accounts/models.py`.

**Code or command from this step**

*1. This line.*

```python
REQUIRED_FIELDS = ['email', 'role']
```

**How did we know what happened?**

This is a learning explanation using small examples, not a second model implementation.

**Where to go next:** Previous: [Step 61 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-61) · Next: [Step 63 — Understanding AbstractUser](01-accounts-app-and-custom-user-model.md#step-63)


---

<a id="step-63"></a>

### Step 63: Understanding AbstractUser

**What was happening before this step?**

We wanted to make sure that using `AbstractUser` did not create an unexplained second user table.

**What did we do, and what does it mean?**

Think of it as borrowing a blueprint rather than copying a completed house. `AbstractUser` supplies definitions; `CustomUser` is the concrete model our project uses. Django creates tables for the concrete model after migrations.

**File or place:** `django.contrib.auth.models (framework reference)`.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```python
class CustomUser(AbstractUser):
    role = models.CharField(max_length=10)
```

**How did we know what happened?**

This was conceptual verification, so the code examples illustrate inheritance rather than new edits.

**Where to go next:** Previous: [Step 62 — Understand CustomUser inheritance](01-accounts-app-and-custom-user-model.md#step-62) · Next: [Step 64 — Inspect Django’s built-in User model](01-accounts-app-and-custom-user-model.md#step-64)


---

<a id="step-64"></a>

### Step 64: Inspect Django’s built-in User model

**What was happening before this step?**

We examined Django's standard user fields so the new model would feel less mysterious.

**What did we do, and what does it mean?**

Django's existing user class defines things such as usernames, names, email and password behaviour. Our model does not need to declare all of these again; it receives them through inheritance.

**File or place:** `Django source (reference only)`.

**Code or command from this step**

*1. Find.*

```python
class User(AbstractUser):
```

**How did we know what happened?**

Reading the framework model is not the same as modifying installed Django files.

**Where to go next:** Previous: [Step 63 — Understanding AbstractUser](01-accounts-app-and-custom-user-model.md#step-63) · Next: [Step 65 — AUTH_USER_MODEL](01-accounts-app-and-custom-user-model.md#step-65)


---

<a id="step-65"></a>

### Step 65: AUTH_USER_MODEL

**What was happening before this step?**

There can be only one main user model for the project's login system. Django needed to know which one we chose.

**What did we do, and what does it mean?**

`AUTH_USER_MODEL` tells Django to use `accounts.CustomUser` whenever it needs the project user type. Without that setting, Django may keep expecting its default user model instead.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

**Understanding the code, one piece at a time**

`AUTH_USER_MODEL = 'accounts.CustomUser'` can be read aloud as “the user class is named `CustomUser` and it lives in the `accounts` app.” It should be chosen early, before the main database has real users.

**How did we know what happened?**

The model was then prepared for the first migration.

**Where to go next:** Previous: [Step 64 — Inspect Django’s built-in User model](01-accounts-app-and-custom-user-model.md#step-64) · Next: [Step 66 — Django Authentication](01-accounts-app-and-custom-user-model.md#step-66)


---

<a id="step-66"></a>

### Step 66: Django Authentication

**What was happening before this step?**

After creating the custom user, we reviewed how the rest of Django's login machinery would use it.

**What did we do, and what does it mean?**

Authentication asks “who is this person?” Django checks usernames and passwords against the configured user model. Role checks, added later, will ask the separate question “what is this person allowed to do?”

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Even if you don't write anything in your project's settings.py, Django's default setting is.*

```python
AUTH_USER_MODEL = "auth.User"
```

**How did we know what happened?**

This conceptual step did not add a new authentication function.

**Where to go next:** Previous: [Step 65 — AUTH_USER_MODEL](01-accounts-app-and-custom-user-model.md#step-65) · Next: [Step 67 — Migration Preparation](01-accounts-app-and-custom-user-model.md#step-67)


---

<a id="step-67"></a>

### Step 67: Migration Preparation

**What was happening before this step?**

The Python user class had been written, but writing a class does not create a table in SQLite.

**What did we do, and what does it mean?**

`makemigrations` prepares the instructions for the database change. The next step is `migrate`, which applies those instructions and creates the actual table.

**File or place:** `accounts/migrations/`.

**Code or command from this step**

*1. Run this in the terminal.*

```powershell
python manage.py makemigrations accounts
```

*2. Example result from the notes (not a command to run).*

```text
Migrations for 'accounts':
  accounts\migrations\0001_initial.py
    + Create model CustomUser
```

**Understanding the code, one piece at a time**

`makemigrations` writes instructions about the database change; it does not change the tables yet. `migrate` is the separate command that applies those instructions.

**How did we know what happened?**

Our notes shows the expected `0001_initial.py` creation message; applying the schema happens later.

**Where to go next:** Previous: [Step 66 — Django Authentication](01-accounts-app-and-custom-user-model.md#step-66) · Next: [Step 68 — Database Reset](02-database-setup-and-admin-site.md#step-68)

---

### The point of this chapter

Every person signs in through the same Django login system, but their role helps us decide which pages to allow later. A model describes the record; it does not become a database table until migrations are run.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

