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
