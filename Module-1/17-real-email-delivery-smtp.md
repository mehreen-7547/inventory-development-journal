# Module 1, Chapter 17: Real email delivery (SMTP)

## Step 170: Real Email Configuration

#### Next: Prepare Gmail SMTP configuration

Our password setup and reset emails currently appear only in the VS Code terminal.

We'll configure **Gmail SMTP** so employees can receive actual emails. It requires no paid API, although Gmail has sending limits.

#### Step 1: Update Django settings

Open **`config/settings.py`**.

Add this import at the top:

```python
import os
```

Find your existing email configuration:

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

Replace those two lines with:

```python
EMAIL_MODE = os.getenv('EMAIL_MODE', 'console')

if EMAIL_MODE == 'smtp':
    EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
    EMAIL_HOST = 'smtp.gmail.com'
    EMAIL_PORT = 587
    EMAIL_USE_TLS = True

    EMAIL_HOST_USER = os.getenv('EMAIL_HOST_USER', '')
    EMAIL_HOST_PASSWORD = os.getenv('EMAIL_HOST_PASSWORD', '')

    DEFAULT_FROM_EMAIL = EMAIL_HOST_USER

else:
    EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
    DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**Keep `PASSWORD_RESET_TIMEOUT` unchanged.**

#### How this works

| Email mode | Behaviour |
|---|---|
| `console` | Emails printed in VS Code terminal |
| `smtp` | Emails sent through Gmail |

  **NEW vs CLASS — `os.getenv()`**

  Reads configuration values from environment variables instead of storing sensitive information directly in Python code.

  This allows us to keep Gmail credentials outside the project files and GitHub repository.

**Save `settings.py`.** The system will continue using console email until we explicitly enable SMTP.

Next, we'll prepare Gmail authentication using an App Password, without putting credentials in the code.

*Previous: Step 169 (Message Styling CSS)  |  Next: Step 171 (Configure SMTP Credentials)*

---

## Step 171: Configure SMTP Credentials

#### Next: Configure Gmail credentials in VS Code

We've generated the Gmail App Password. Now we'll provide it to Django securely, without adding it to our project files.

#### Step 1: Open the VS Code terminal

If your Django server is running, stop it using `Ctrl + C`.

In the **PowerShell terminal**, run these commands:

```powershell
$env:EMAIL_MODE = "smtp"

$env:EMAIL_HOST_USER = "mehreen7547@gmail.com"

$secure = Read-Host "Enter Gmail App Password" -AsSecureString

$env:EMAIL_HOST_PASSWORD = ([System.Net.NetworkCredential]::new("", $secure).Password) -replace ' ', ''

Remove-Variable secure
```

When PowerShell asks for your App Password, paste it and press Enter. The password will not be displayed while you type.

#### Step 2: Start Django

In the **same terminal**, run:

```powershell
python manage.py runserver
```

#### What have we configured?

| Variable | Purpose |
|---|---|
| `EMAIL_MODE` | Enables SMTP delivery |
| `EMAIL_HOST_USER` | Gmail sending account |
| `EMAIL_HOST_PASSWORD` | Gmail App Password |

  **NEW vs CLASS — Environment variables**

  Environment variables allow Django to access configuration without hardcoding passwords.

  These variables exist only in the current PowerShell session. If you close that terminal, you'll need to set them again.

  This is suitable for our development and testing stage.

**Expected result:** Django starts normally. No email will be sent until we trigger an email operation.

Next, we'll send a real test email and verify that it arrives in your Gmail inbox.

*Previous: Step 170 (Real Email Configuration)  |  Next: Step 172 (Install python-dotenv)*

---

## Step 172: Install python-dotenv

Yes. We can save them permanently for development using a **`.env` file**. This is preferable to entering credentials every time you open PowerShell.

The file stays on your computer and is excluded from GitHub.

#### Step 1: Install `python-dotenv`

In your VS Code terminal, run:

```powershell
python -m pip install python-dotenv
```

#### Step 2: Create a `.env` file

Create it in your project root, alongside `manage.py`.

    **techmindz_inventory_module0_sqlite_atomic/**

      manage.py

      **.env**

      .gitignore

      config/

Inside `.env`, add:

```dotenv
EMAIL_MODE=smtp
EMAIL_HOST_USER=mehreen7547@gmail.com
EMAIL_HOST_PASSWORD=YOUR_16_CHARACTER_APP_PASSWORD
```

Replace the placeholder with your Gmail App Password, without spaces.

#### Step 3: Update `config/settings.py`

At the top, add:

```python
from dotenv import load_dotenv
```

Find the existing `BASE_DIR` definition:

```python
BASE_DIR = Path(__file__).resolve().parent.parent
```

Immediately below it, add:

```python
load_dotenv(BASE_DIR / '.env')
```

Keep the existing SMTP configuration using `os.getenv()` unchanged.

#### Step 4: Protect your credentials

Open `.gitignore` and add:

```gitignore
.env
```

This prevents Git from adding the file in future commits, provided it wasn't already tracked. You can verify with `git status --ignored --short .env` — it should show `!! .env`.

**Important:** `.env` stores credentials as plain text. Keep it private, never upload it, and don't share its contents.

#### Result

Django will automatically read the credentials whenever it starts, including after restarting VS Code or your computer. You won't need to re-enter them in PowerShell.

*Previous: Step 171 (Configure SMTP Credentials)  |  Next: Step 173 (Fix Email Backend)*

---
