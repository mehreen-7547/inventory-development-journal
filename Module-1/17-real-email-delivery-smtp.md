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

## Step 173: Fix Email Backend

**Found the problem. Django is still using the Console Email Backend, not Gmail SMTP.**

Your output confirms this:

```text
From: no-reply@example.com
```

Also, the entire email was printed in your terminal. That is console-backend behaviour.

The `1` means Django successfully processed one email using the configured backend. **It does not mean Gmail sent it.**

#### Step 1: Check `config/settings.py`

At the top, ensure you have:

```python
import os
from dotenv import load_dotenv
```

Immediately below your existing `BASE_DIR` definition:

```python
load_dotenv(BASE_DIR / '.env')
```

Then verify your email configuration contains:

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

Make sure your `.env` file is saved alongside `manage.py` and contains `EMAIL_MODE=smtp`.

#### Step 2: Verify

Exit Django Shell:

```python
exit()
```

Start a new Django Shell:

```powershell
python manage.py shell
```

Run:

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

Expected:

```text
smtp
django.core.mail.backends.smtp.EmailBackend
mehreen7547@gmail.com
```

If it still shows `console`, an existing PowerShell environment variable may be overriding your `.env` file.

**Do not send another test email until these three values are correct.**

*Previous: Step 172 (Install python-dotenv)  |  Next: Step 174 (Fix the email configuration)*

---

## Step 174: Fix the email configuration

**Found the issue.** Your `settings.py` is correctly loading `.env`, but the last two lines are forcing Django to use the Console Email Backend.

#### Step 289 — Fix the email configuration

In `config/settings.py`, find these lines at the bottom:

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

**Replace those three lines** with:

```python
# Email configuration
EMAIL_MODE = os.getenv("EMAIL_MODE", "console").strip().lower()

if EMAIL_MODE == "smtp":
    EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"
    EMAIL_HOST = "smtp.gmail.com"
    EMAIL_PORT = 587
    EMAIL_USE_TLS = True

    EMAIL_HOST_USER = os.getenv("EMAIL_HOST_USER", "")
    EMAIL_HOST_PASSWORD = os.getenv("EMAIL_HOST_PASSWORD", "")

    DEFAULT_FROM_EMAIL = EMAIL_HOST_USER
else:
    EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
    DEFAULT_FROM_EMAIL = "no-reply@example.com"

PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

Your `.env` file should contain these entries alongside its existing settings:

```dotenv
EMAIL_MODE=smtp
EMAIL_HOST_USER=mehreen7547@gmail.com
EMAIL_HOST_PASSWORD=YOUR_GMAIL_APP_PASSWORD
```

Replace the password placeholder with your actual App Password. Do not share it or commit `.env` to GitHub.

#### Verify

Restart Django Shell and run:

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

Expected output:

```text
smtp
django.core.mail.backends.smtp.EmailBackend
mehreen7547@gmail.com
```

No other changes to your `settings.py` are required for this fix.

*Previous: Step 173 (Fix Email Backend)  |  Next: Step 175 (Change manage.py)*

---

## Step 175: Change manage.py

#### 1. Exit the current Django Shell

In your VS Code terminal, where you see `>>>`, type:

```python
exit()
```

Press **Enter**.

You should now see the PowerShell prompt:

```text
(.venv) PS C:\Users\mehre\...>
```

#### 2. Start Django Shell again

Type:

```powershell
python manage.py shell
```

Press **Enter**. You'll see `>>>` again.

#### 3. Check the email configuration

Copy and paste these lines:

```python
from django.conf import settings
print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

Press **Enter**.

Expected result:

```text
smtp
django.core.mail.backends.smtp.EmailBackend
mehreen7547@gmail.com
```

**Send me the three lines of output you get.**

*Previous: Step 174 (Fix the email configuration)  |  Next: Step 176 (Send a real email through Gmail)*

---
