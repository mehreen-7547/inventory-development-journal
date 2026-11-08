# MODULE 1 — Accounts

## Chapter 17: Real email delivery (SMTP) (Steps 170–178)

**Where we are.** Console email helped test content but could not deliver a message to a real employee. We configured Gmail SMTP and resolved settings errors.

**What this chapter gives us.** Separate credentials from code, load .env, remove conflicting backend assignments, and test delivery.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain console versus SMTP backends and what send_mail returning 1 does and does not prove.

### Find a step

- [Step 170: Real Email Configuration](#step-170)
- [Step 171: Configure SMTP Credentials](#step-171)
- [Step 172: Install python-dotenv](#step-172)
- [Step 173: Fix Email Backend](#step-173)
- [Step 174: Remove the conflicting email backend override](#step-174)
- [Step 175: Restart Django Shell after configuration changes](#step-175)
- [Step 176: Send a real email through Gmail](#step-176)
- [Step 177: Plan the shared layout](#step-177)
- [Step 178: Create the shared base.html template](#step-178)

---

<a id="step-170"></a>

### Step 170: Real Email Configuration

**Why we are doing this.** Until now email appeared in the terminal. Actual onboarding requires the ability to deliver messages to the employee's inbox.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this import at the top.*

```python
import os
```

*2. Recorded snippet; Find your existing email configuration.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

*3. Recorded snippet; Replace those two lines with.*

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

**How it works.** The settings begin to support SMTP configuration through environment variables. Django's email backend can switch between local console mode and authenticated Gmail SMTP.

**Follow the flow.** `EMAIL_MODE` selects one of two backends. Console is the safe default for development. When it equals `smtp`, Django uses Gmail's SMTP server on port 587 with TLS and reads the sender and app password from environment variables. Real credentials are local settings; they must not be typed into tracked code.

**How we checked it.** This is configuration preparation; a genuine external delivery test occurs in Step 176.

**Connects to.** Previous: [Step 169 — Message Styling CSS](16-message-styling.md#step-169) · Next: [Step 171 — Configure SMTP Credentials](17-real-email-delivery-smtp.md#step-171)


---

<a id="step-171"></a>

### Step 171: Configure SMTP Credentials

**Why we are doing this.** Gmail SMTP needs credentials, but those secrets must not be committed to either repository.

**File / location.** `PowerShell session variables`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In the PowerShell terminal, run these commands.*

```powershell
$env:EMAIL_MODE = "smtp"

$env:EMAIL_HOST_USER = "youraddress@gmail.com"

$secure = Read-Host "Enter Gmail App Password" -AsSecureString

$env:EMAIL_HOST_PASSWORD = ([System.Net.NetworkCredential]::new("", $secure).Password) -replace ' ', ''

Remove-Variable secure
```

*2. Recorded snippet; In the same terminal, run.*

```powershell
python manage.py runserver
```

**How it works.** The PowerShell session variables provide an email address and app password to the running process. Reading the app password securely avoids placing it visibly in the command line.

**How we checked it.** The next steps replace temporary session variables with a local `.env` approach.

**Connects to.** Previous: [Step 170 — Real Email Configuration](17-real-email-delivery-smtp.md#step-170) · Next: [Step 172 — Install python-dotenv](17-real-email-delivery-smtp.md#step-172)


---

<a id="step-172"></a>

### Step 172: Install python-dotenv

**Why we are doing this.** Re-entering SMTP variables in every terminal session was inconvenient, and sensitive settings should remain local.

**File / location.** `.env and config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Inside .env, add.*

```ini
EMAIL_MODE=smtp
EMAIL_HOST_USER=youraddress@gmail.com
EMAIL_HOST_PASSWORD=YOUR_16_CHARACTER_APP_PASSWORD
```

*2. Recorded snippet; Find the existing BASEDIR definition.*

```python
BASE_DIR = Path(__file__).resolve().parent.parent
```

*3. Recorded snippet; In your VS Code terminal, run.*

```powershell
python -m pip install python-dotenv
```

**How it works.** Installing `python-dotenv` allows Django settings to read values from a private `.env` file. The example entries are placeholders, never real passwords to publish.

**Follow the flow.** `python-dotenv` loads key-value pairs from `.env` into the process environment. Those values become available to `os.getenv` in `settings.py`. Loading them must happen *before* the email settings read them. `.env` belongs in `.gitignore`; `.env.example` should use placeholders only.

**How we checked it.** The following steps diagnose why console delivery was still being used despite those settings.

**Connects to.** Previous: [Step 171 — Configure SMTP Credentials](17-real-email-delivery-smtp.md#step-171) · Next: [Step 173 — Fix Email Backend](17-real-email-delivery-smtp.md#step-173)


---

<a id="step-173"></a>

### Step 173: Fix Email Backend

**Why we are doing this.** A test email was printed in the terminal rather than sent, showing Django had not switched to SMTP.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

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

*2. Recorded snippet; Run.*

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

*3. Recorded snippet.*

```python
import os
from dotenv import load_dotenv
```

**How it works.** `load_dotenv` must run before environment variables are read; the selected `EMAIL_BACKEND` determines whether mail goes to console or an SMTP server.

This setting selects whether Django prints email locally or tries to deliver it through SMTP.

**How we checked it.** The source explicitly identifies console-backend output, so this step is a configuration diagnosis rather than proof of delivery.

**Connects to.** Previous: [Step 172 — Install python-dotenv](17-real-email-delivery-smtp.md#step-172) · Next: [Step 174 — Remove the conflicting email backend override](17-real-email-delivery-smtp.md#step-174)


---

<a id="step-174"></a>

### Step 174: Remove the conflicting email backend override

**Why we are doing this.** The settings file contained a later assignment that overwrote the new SMTP choice with the console backend.

**File / location.** `config/settings.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In config/settings.py, find these lines at the bottom.*

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

*2. Recorded snippet; Replace those three lines with.*

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

*3. Recorded snippet; Your .env file should contain these entries alongside its existing settings.*

```ini
EMAIL_MODE=smtp
EMAIL_HOST_USER=youraddress@gmail.com
EMAIL_HOST_PASSWORD=YOUR_GMAIL_APP_PASSWORD
```

*4. Recorded snippet; Restart Django Shell and run.*

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

*5. Recorded example/output — **do not type this as an instruction**; Expected output.*

```text
smtp
django.core.mail.backends.smtp.EmailBackend
youraddress@gmail.com
```

**How it works.** Removing the unconditional override leaves the conditional `EMAIL_MODE` configuration in control. In Python, the last assignment to a variable wins.

**Follow the flow.** The key debugging idea is assignment order. Even a correct conditional SMTP block fails if later lines set `EMAIL_BACKEND` back to console. Python uses the last assigned value. Removing the unconditional override makes the environment-selected backend effective, without changing the rest of the Django mail code.

**How we checked it.** The updated settings are followed by a fresh Django shell and delivery test.

**Connects to.** Previous: [Step 173 — Fix Email Backend](17-real-email-delivery-smtp.md#step-173) · Next: [Step 175 — Restart Django Shell after configuration changes](17-real-email-delivery-smtp.md#step-175)


---

<a id="step-175"></a>

### Step 175: Restart Django Shell after configuration changes

**Why we are doing this.** After correcting settings, the old interactive Django shell could still hold the previous configuration in memory.

**File / location.** `Django shell (restart)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Copy and paste these lines.*

```python
from django.conf import settings
print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

**How it works.** Exit the current shell and start a new one so Django loads settings afresh. `manage.py` is the launcher used to open that shell, not a file to rewrite here.

This setting selects whether Django prints email locally or tries to deliver it through SMTP.

**How we checked it.** The next step sends a test email from the fresh environment.

**Connects to.** Previous: [Step 174 — Remove the conflicting email backend override](17-real-email-delivery-smtp.md#step-174) · Next: [Step 176 — Send a real email through Gmail](17-real-email-delivery-smtp.md#step-176)


---

<a id="step-176"></a>

### Step 176: Send a real email through Gmail

**Why we are doing this.** The final question was whether Gmail could deliver mail, not merely whether Django reported sending it.

**File / location.** `Django shell (send_mail)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In the same Django Shell, where you see >>>, paste.*

```python
from django.core.mail import send_mail
from django.conf import settings

result = send_mail(
    'Techmindz SMTP Test',
    'This is a real email sent from Django using Gmail SMTP.',
    settings.DEFAULT_FROM_EMAIL,
    ['example+sales1@gmail.com'],
    fail_silently=False
)

print(result)
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output.*

```text
1
```

*3. Recorded example/output — **do not type this as an instruction**.*

```text
in:anywhere subject:"Techmindz SMTP Test"
```

**How it works.** `send_mail` calls the configured backend. A return value of `1` means Django accepted one message for sending; inbox arrival should also be confirmed separately.

**Follow the flow.** The Django shell uses the project's settings, so `send_mail` calls the configured backend. The return value counts messages accepted for processing by Django's email function; it is not a receipt from the employee. To verify actual SMTP delivery, check that SMTP mode was active, the command did not raise an error, and the message arrived in the intended inbox.

**How we checked it.** The original notes show the send result and subsequent checks. Do not interpret a console backend's `1` as proof of SMTP delivery.

**Connects to.** Previous: [Step 175 — Restart Django Shell after configuration changes](17-real-email-delivery-smtp.md#step-175) · Next: [Step 177 — Plan the shared layout](17-real-email-delivery-smtp.md#step-177)


---

<a id="step-177"></a>

### Step 177: Plan the shared layout

**Why we are doing this.** With the functional account workflows in place, we started consolidating repeated page elements and clarifying access handling.

**File / location.** `template/layout planning`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Replace it with.*

```python
if request.user.role not in roles:
    messages.error(request, 'You do not have permission to access this page.')
    return redirect('home')
```

**How it works.** The notes discuss shared template/navigation decisions and how permission errors should surface. The main shared base template is implemented in the next step.

The message is queued so the next rendered page can explain an error.

**How we checked it.** This is a transition and review step; it is not evidence that every template was already converted.

**Connects to.** Previous: [Step 176 — Send a real email through Gmail](17-real-email-delivery-smtp.md#step-176) · Next: [Step 178 — Create the shared base.html template](17-real-email-delivery-smtp.md#step-178)


---

<a id="step-178"></a>

### Step 178: Create the shared base.html template

**Why we are doing this.** Login, homepage, and employee screens each repeated header and message markup; a common layout would keep them consistent.

**File / location.** `templates/base.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Create this new file in the main templates folder, then paste.*

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block title %}Inventory System{% endblock %}</title>

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
          rel="stylesheet">

    {% block extra_css %}{% endblock %}
</head>
<body>

<nav class="navbar bg-body-tertiary border-bottom">
    <div class="container">
        <a class="navbar-brand" href="{% url 'home' %}">
            Inventory System
        </a>

        {% if user.is_authenticated %}
        <div class="d-flex align-items-center gap-3 flex-wrap">

            {% if user.role == 'admin' %}
            <a href="{% url 'employee_list' %}">
                Manage Employees
            </a>
            {% endif %}

            <a href="{% url 'change_password' %}">
                Change Password
            </a>

            <form method="post" action="{% url 'logout' %}">
                {% csrf_token %}
                <button type="submit" class="btn btn-outline-secondary btn-sm">
                    Logout
                </button>
            </form>

        </div>
        {% endif %}
    </div>
</nav>

<main class="container py-4">

    {% if messages %}
        {% for message in messages %}
            <div class="alert {% if 'error' in message.tags %}alert-danger{% elif 'success' in message.tags %}alert-success{% else %}alert-info{% endif %}">
                {{ message }}
            </div>
        {% endfor %}
    {% endif %}

    {% block content %}{% endblock %}

</main>

</body>
</html>
```

**How it works.** `base.html` defines shared HTML, navigation, Bootstrap/static assets, and `{% block %}` placeholders. Child templates override only the content they need.

**Follow the flow.** `base.html` contains the shared page skeleton: document head, stylesheet, navigation, message display, and named blocks. Each page can extend this layout and replace only `{% block content %}` or `{% block title %}`. This avoids copying the same header and navigation into every account template.

**How we checked it.** The individual pages still have to be updated to extend the base template in later steps.

**Connects to.** Previous: [Step 177 — Plan the shared layout](17-real-email-delivery-smtp.md#step-177) · Next: [Step 179 — Dashboard Template Inheritance](18-shared-base-template-and-page-styling.md#step-179)

---

**Chapter recap.** Separate credentials from code, load .env, remove conflicting backend assignments, and test delivery.

**Practice explaining:** Explain console versus SMTP backends and what send_mail returning 1 does and does not prove.

