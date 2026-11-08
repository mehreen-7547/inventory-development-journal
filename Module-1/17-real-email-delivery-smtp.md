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
