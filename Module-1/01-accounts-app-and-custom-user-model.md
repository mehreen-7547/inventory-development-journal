# MODULE 1 — Accounts

## Chapter 1: Accounts app and custom user model (Steps 56–67)

**Where we are.** We now needed accounts for three kinds of users: Admin, Inventory Manager, and Sales Staff. Django's default user needed a role field before login pages could be built.

**What this chapter gives us.** Create an accounts app, define CustomUser, and set AUTH_USER_MODEL before the first user migration.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain AbstractUser inheritance and the difference between a model class and a database table.

### Find a step

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

**Why we are doing this.** Module 0 established the Django foundation. Module 1 starts the real account system so administrators, managers, and sales staff can sign in with appropriate permissions.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In your VS Code PowerShell terminal, run.*

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

**How it works.** The notes review the approved account workflow and prepare the project structure. This is a planning-to-implementation transition, not a finished authentication feature.

**How we checked it.** No code test was expected before the account models and views were created.

**Connects to.** Previous: [Step 55 — Publish Completion Tag](../Module-0/07-module-0-completion.md#step-55) · Next: [Step 57 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-57)


---

<a id="step-57"></a>

### Step 57: Custom User Model

**Why we are doing this.** Django's default user model did not include the business role we needed for this inventory application.

**File / location.** `accounts/models.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open accounts/models.py in VS Code and replace its contents with.*

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

**How it works.** `CustomUser` extends `AbstractUser`, retaining Django's username and password machinery while adding `role`. The choices represent Admin, Inventory Manager, and Sales Staff.

**Follow the flow.** `AbstractUser` gives this class Django's existing username, password hashing, and account flags. `ROLE_CHOICES` limits the business-role labels to three values. `role = models.CharField(...)` is the extra database field. `default='sales'` supplies a starting value. `REQUIRED_FIELDS` controls extra prompts in the terminal's superuser creation command.

**How we checked it.** The model definition was proposed here; migrations and actual account tests come in later steps.

**Connects to.** Previous: [Step 56 — Begin the Accounts module](01-accounts-app-and-custom-user-model.md#step-56) · Next: [Step 58 — Clarify account creation workflow](01-accounts-app-and-custom-user-model.md#step-58)


---

<a id="step-58"></a>

### Step 58: Clarify account creation workflow

**Why we are doing this.** Before changing the database, we corrected the intended workflow so account creation and privileges matched the agreed design.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; In your VS Code PowerShell terminal, run.*

```powershell
Set-Location "C:\Users\YOUR_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"

python manage.py startapp accounts
```

**How it works.** The revised approach uses an explicit custom user model and separates user roles from Django's built-in staff/superuser flags. These concepts overlap but are not identical.

**How we checked it.** This is a design correction in the source, not a separate runtime operation.

**Connects to.** Previous: [Step 57 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-57) · Next: [Step 59 — App Creation](01-accounts-app-and-custom-user-model.md#step-59)


---

<a id="step-59"></a>

### Step 59: App Creation

**Why we are doing this.** Django needs an app to own the new user model, forms, views, and URLs.

**File / location.** `accounts/ (Django app)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; You've run.*

```powershell
cd "C:\Users\YOUR_USERNAME\projects2026\Techmindz_Inventory_Module0_SQLite_Atomic_Update\techmindz_inventory_module0_sqlite_atomic"
python manage.py startapp accounts
```

**How it works.** `startapp accounts` generates the standard Django app files. Registering the app later tells Django to include its models and configuration.

**Follow the flow.** Django creates folders and starting files for the app, including `models.py`, `views.py`, `admin.py`, and `migrations/`. The generated files do not implement our account rules yet. They are places where the next steps will put the real code.

**How we checked it.** The following steps add settings and model code; no independent test was recorded here.

**Connects to.** Previous: [Step 58 — Clarify account creation workflow](01-accounts-app-and-custom-user-model.md#step-58) · Next: [Step 60 — Configure Settings](01-accounts-app-and-custom-user-model.md#step-60)


---

<a id="step-60"></a>

### Step 60: Configure Settings

**Why we are doing this.** Creating the `accounts` folder alone does not make Django load it or use its custom user class.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find INSTALLEDAPPS and add "accounts", immediately after "core",.*

```python
    "core",
    "accounts",
]
```

*2. Recorded snippet; Immediately below the closing ], add.*

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

**How it works.** `INSTALLED_APPS` enables the app; `AUTH_USER_MODEL` points authentication at `accounts.CustomUser`. The setting must be decided before initial user-table migrations.

**Follow the flow.** `INSTALLED_APPS` tells Django to discover the accounts app. `AUTH_USER_MODEL` tells Django which concrete user table to use for authentication. We need the second setting before creating our first real user migration because changing user models after populating a database is difficult.

**How we checked it.** The next step defines the model itself. This setting should be checked before migration.

**Connects to.** Previous: [Step 59 — App Creation](01-accounts-app-and-custom-user-model.md#step-59) · Next: [Step 61 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-61)


---

<a id="step-61"></a>

### Step 61: Custom User Model

**Why we are doing this.** With the app registered, we put the custom user specification into the actual Django model file.

**File / location.** `accounts/models.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace its default contents with.*

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

**How it works.** `CustomUser(AbstractUser)` inherits default fields and adds the role field with fixed choices. It becomes the application's user table once migrations run.

**Follow the flow.** This is the first actual model definition in the `accounts` app. Inheritance means we do not re-create fields like `username` and `password`. The role field adds our business distinction. The class remains a description until Django generates and applies its migrations.

**How we checked it.** The source records the code but not a database verification at this exact step.

**Connects to.** Previous: [Step 60 — Configure Settings](01-accounts-app-and-custom-user-model.md#step-60) · Next: [Step 62 — Understand CustomUser inheritance](01-accounts-app-and-custom-user-model.md#step-62)


---

<a id="step-62"></a>

### Step 62: Understand CustomUser inheritance

**Why we are doing this.** The new model syntax needed explanation before running migrations, because inheritance can look like the code is magically creating fields.

**File / location.** `accounts/models.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; This line.*

```python
REQUIRED_FIELDS = ['email', 'role']
```

**How it works.** `CustomUser` is a Python class; its parent `AbstractUser` already declares standard account fields. Adding `role` changes the schema Django will build for this project.

**How we checked it.** This is a learning explanation using small examples, not a second model implementation.

**Connects to.** Previous: [Step 61 — Custom User Model](01-accounts-app-and-custom-user-model.md#step-61) · Next: [Step 63 — Understanding AbstractUser](01-accounts-app-and-custom-user-model.md#step-63)


---

<a id="step-63"></a>

### Step 63: Understanding AbstractUser

**Why we are doing this.** We clarified what is inherited from `AbstractUser` and why it is not itself a separate database table.

**File / location.** `django.contrib.auth.models (framework reference)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```python
class CustomUser(AbstractUser):
    role = models.CharField(max_length=10)
```

**How it works.** `AbstractUser` supplies username, password handling, and account flags as class definitions; Django creates the concrete table for our `CustomUser` model.

**How we checked it.** This was conceptual verification, so the code examples illustrate inheritance rather than new edits.

**Connects to.** Previous: [Step 62 — Understand CustomUser inheritance](01-accounts-app-and-custom-user-model.md#step-62) · Next: [Step 64 — Inspect Django’s built-in User model](01-accounts-app-and-custom-user-model.md#step-64)


---

<a id="step-64"></a>

### Step 64: Inspect Django’s built-in User model

**Why we are doing this.** To understand which fields are available automatically, we looked at how Django declares its built-in user model.

**File / location.** `Django source (reference only)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Find.*

```python
class User(AbstractUser):
```

**How it works.** Django's source defines `User` as a subclass of `AbstractUser`. Our custom class follows the same principle but adds application-specific data.

**How we checked it.** Reading the framework model is not the same as modifying installed Django files.

**Connects to.** Previous: [Step 63 — Understanding AbstractUser](01-accounts-app-and-custom-user-model.md#step-63) · Next: [Step 65 — AUTH_USER_MODEL](01-accounts-app-and-custom-user-model.md#step-65)


---

<a id="step-65"></a>

### Step 65: AUTH_USER_MODEL

**Why we are doing this.** Django needed a single authoritative answer to the question: which user model should every authentication feature use?

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```python
AUTH_USER_MODEL = "accounts.CustomUser"
```

**How it works.** `AUTH_USER_MODEL = 'accounts.CustomUser'` tells Django's authentication and foreign-key machinery to use our class instead of the default `auth.User` model.

**How we checked it.** The model was then prepared for the first migration.

**Connects to.** Previous: [Step 64 — Inspect Django’s built-in User model](01-accounts-app-and-custom-user-model.md#step-64) · Next: [Step 66 — Django Authentication](01-accounts-app-and-custom-user-model.md#step-66)


---

<a id="step-66"></a>

### Step 66: Django Authentication

**Why we are doing this.** We connected the custom-user decision to the broader login system rather than treating it as an isolated setting.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Even if you don't write anything in your project's settings.py, Django's default setting is.*

```python
AUTH_USER_MODEL = "auth.User"
```

**How it works.** Django provides password hashing, login sessions, and account conventions; our custom model supplies the user fields it works with. We reuse these existing security features.

**How we checked it.** This conceptual step did not add a new authentication function.

**Connects to.** Previous: [Step 65 — AUTH_USER_MODEL](01-accounts-app-and-custom-user-model.md#step-65) · Next: [Step 67 — Migration Preparation](01-accounts-app-and-custom-user-model.md#step-67)


---

<a id="step-67"></a>

### Step 67: Migration Preparation

**Why we are doing this.** Once the custom model and setting existed, Django had to translate the Python class into migration instructions.

**File / location.** `accounts/migrations/`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in the terminal.*

```powershell
python manage.py makemigrations accounts
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output.*

```text
Migrations for 'accounts':
  accounts\migrations\0001_initial.py
    + Create model CustomUser
```

**How it works.** `makemigrations accounts` creates an initial migration file describing the new table. It does not apply those instructions to the database yet.

**Follow the flow.** `makemigrations` reads the Python model and writes a migration describing how a table should look. It does not create that table immediately. The matching `migrate` command in a later step performs the actual database work.

**How we checked it.** The source shows the expected `0001_initial.py` creation message; applying the schema happens later.

**Connects to.** Previous: [Step 66 — Django Authentication](01-accounts-app-and-custom-user-model.md#step-66) · Next: [Step 68 — Database Reset](02-database-setup-and-admin-site.md#step-68)

---

**Chapter recap.** Create an accounts app, define CustomUser, and set AUTH_USER_MODEL before the first user migration.

**Practice explaining:** Explain AbstractUser inheritance and the difference between a model class and a database table.

