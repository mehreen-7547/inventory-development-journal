# MODULE 1 — Employee accounts

## Chapter 17: Real email delivery (SMTP) (Steps 170–178)

### Before we begin

Until now, an email only appeared as text in our VS Code terminal. That let us examine links, but it could not send a welcome message to a real employee.

### What we worked on

We tried using Gmail to send messages, learned where to keep the private Gmail details, found why Django kept printing messages in the terminal and corrected the settings. We then tested sending again.

### Picture it this way

Imagine the difference between writing a letter on your own desk and posting it to someone. “Console mode” leaves the letter on our desk. “SMTP mode” asks Gmail to deliver it. The chapter follows the mistakes we encountered while switching between those two modes. Steps 177–178 start the next topic: a common design for the website's pages.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

We had been testing emails by printing their contents in the VS Code terminal. That was useful for development, but a real employee cannot see our terminal. To receive a welcome email, the message must reach their inbox.

**What did we do, and what does it mean?**

We added a choice in Django's settings: keep printing emails during testing, or send them through Gmail. Gmail accepts messages from programs through a service called SMTP. We also told Django where the Gmail address and password would come from, without typing those private values into Python code.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Add this import at the top.*

```python
import os
```

*2. Find your existing email configuration.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

*3. Replace those two lines with.*

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

**Understanding the code, one piece at a time**

Read the setting like a choice between two roads. `EMAIL_MODE` asks which road to take. For `console`, Django prints email in VS Code. For `smtp`, Django connects to Gmail. `os.getenv(...)` reads values supplied privately through the computer rather than from hard-coded strings in the shared Python file. `EMAIL_HOST_USER` is the sending address; `EMAIL_HOST_PASSWORD` is its special app password.

**How did we know what happened?**

We had only changed settings at this point; a genuine external delivery test occurs in Step 176.

**Small example:** If `EMAIL_MODE` is `console`, Django prints the message on your laptop. If it is `smtp`, Django tries to pass the message to Gmail. Both choices use the same welcome-email helper.

**Where to go next:** Previous: [Step 169 — Message Styling CSS](16-message-styling.md#step-169) · Next: [Step 171 — Configure SMTP Credentials](17-real-email-delivery-smtp.md#step-171)


---

<a id="step-171"></a>

### Step 171: Configure SMTP Credentials

**What was happening before this step?**

Once Django knew how to send through Gmail, it needed the details of which Gmail account to use. Those details included a special password, so we did not want to put them in a file shared on GitHub.

**What did we do, and what does it mean?**

For this early test, the terminal asked for the Gmail app password and held it only during that terminal session. Django could then read it while running. Closing the terminal would lose those temporary settings, which led to the next step.

**File or place:** `PowerShell session variables`.

**Code or command from this step**

*1. In the PowerShell terminal, run these commands.*

```powershell
$env:EMAIL_MODE = "smtp"

$env:EMAIL_HOST_USER = "youraddress@gmail.com"

$secure = Read-Host "Enter Gmail App Password" -AsSecureString

$env:EMAIL_HOST_PASSWORD = ([System.Net.NetworkCredential]::new("", $secure).Password) -replace ' ', ''

Remove-Variable secure
```

*2. In the same terminal, run.*

```powershell
python manage.py runserver
```

**Understanding the code, one piece at a time**

`$env:EMAIL_MODE = "smtp"` tells programs started from that PowerShell window to use Gmail mode. The `Read-Host ... -AsSecureString` line asks for the app password without plainly displaying it while typed. This was a temporary method; the next step makes the setting easier to reuse.

**How did we know what happened?**

The next steps replace temporary session variables with a local `.env` approach.

**Small example:** The employee never sees the special Gmail password. It belongs only to the Gmail account we use to send outgoing messages, not to the employee account being created.

**Where to go next:** Previous: [Step 170 — Real Email Configuration](17-real-email-delivery-smtp.md#step-170) · Next: [Step 172 — Install python-dotenv](17-real-email-delivery-smtp.md#step-172)


---

<a id="step-172"></a>

### Step 172: Install python-dotenv

**What was happening before this step?**

Typing the Gmail details again every time was inconvenient. We needed somewhere on this computer to keep them without uploading them to GitHub.

**What did we do, and what does it mean?**

We used a file named `.env`, which is just a small private settings file containing lines like `EMAIL_MODE=smtp`. The `python-dotenv` package reads that file when Django starts. This only works if Django loads the file before it asks what email mode to use.

**File or place:** `.env and config/settings.py`.

**Code or command from this step**

*1. Inside .env, add.*

```ini
EMAIL_MODE=smtp
EMAIL_HOST_USER=youraddress@gmail.com
EMAIL_HOST_PASSWORD=YOUR_16_CHARACTER_APP_PASSWORD
```

*2. Find the existing BASEDIR definition.*

```python
BASE_DIR = Path(__file__).resolve().parent.parent
```

*3. In your VS Code terminal, run.*

```powershell
python -m pip install python-dotenv
```

**Understanding the code, one piece at a time**

The three lines in `.env` mean “send real email,” “use this Gmail address,” and “use this special Gmail app password.” Installing `python-dotenv` lets Python read those lines. The important order is: load `.env` first, then read values with `os.getenv`.

**How did we know what happened?**

The following steps diagnose why console delivery was still being used despite those settings.

**Small example:** `.env` is like a note kept on your own desk. It can say which email account to use, while `settings.py` contains the general instructions for reading that note.

**Where to go next:** Previous: [Step 171 — Configure SMTP Credentials](17-real-email-delivery-smtp.md#step-171) · Next: [Step 173 — Fix Email Backend](17-real-email-delivery-smtp.md#step-173)


---

<a id="step-173"></a>

### Step 173: Fix Email Backend

**What was happening before this step?**

We tried sending an email, but the message still appeared in the terminal. That told us Django was behaving as if testing mode were still selected.

**What did we do, and what does it mean?**

We checked what email mode Django was actually using, instead of assuming the new settings worked. The investigation pointed to when `.env` was loaded. If the settings are read before that file is loaded, Django cannot see the Gmail details and may use the default terminal mode.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. Code or command used at this point.*

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

*2. Run.*

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

*3. Code or command used at this point.*

```python
import os
from dotenv import load_dotenv
```

**Understanding the code, one piece at a time**

The `print(settings.EMAIL_BACKEND)` check tells us what Django actually chose. If it displays a name ending in `console.EmailBackend`, the app is still printing mail locally. A name ending in `smtp.EmailBackend` means it is configured to try Gmail, though we still need a delivery test.

**How did we know what happened?**

Our notes explicitly identifies console-backend output, so this step is a configuration diagnosis rather than proof of delivery.

**Small example:** If Django reads the email option before opening `.env`, it may assume `console`. This explains why an email could still print in VS Code even after we typed Gmail details into `.env`.

**Where to go next:** Previous: [Step 172 — Install python-dotenv](17-real-email-delivery-smtp.md#step-172) · Next: [Step 174 — Remove the conflicting email backend override](17-real-email-delivery-smtp.md#step-174)


---

<a id="step-174"></a>

### Step 174: Remove the conflicting email backend override

**What was happening before this step?**

Even after dealing with the private settings file, the email still used the wrong mode. We found another cause in `settings.py`.

**What did we do, and what does it mean?**

The file was setting the email option twice: first in the new “Gmail or terminal” choice, then later again to “terminal.” Python keeps the last value it sees, so the later line cancelled our Gmail setting. Removing that extra line allowed the original choice to work.

**File or place:** `config/settings.py`.

**Code or command from this step**

*1. In config/settings.py, find these lines at the bottom.*

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

*2. Replace those three lines with.*

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

*3. Your .env file should contain these entries alongside its existing settings.*

```ini
EMAIL_MODE=smtp
EMAIL_HOST_USER=youraddress@gmail.com
EMAIL_HOST_PASSWORD=YOUR_GMAIL_APP_PASSWORD
```

*4. Restart Django Shell and run.*

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

*5. Example result from the notes (not a command to run).*

```text
smtp
django.core.mail.backends.smtp.EmailBackend
youraddress@gmail.com
```

**Understanding the code, one piece at a time**

Python reads the settings file from top to bottom. If we set `EMAIL_BACKEND` to Gmail and later set it to console again, the later value wins. Removing the extra console assignment fixes the clash without changing how the email helper builds messages.

**How did we know what happened?**

The updated settings are followed by a fresh Django shell and delivery test.

**Small example:** Imagine writing `mode = "gmail"` and then, further down, `mode = "terminal"`. The second line wins. That was the kind of conflict we removed.

**Where to go next:** Previous: [Step 173 — Fix Email Backend](17-real-email-delivery-smtp.md#step-173) · Next: [Step 175 — Restart Django Shell after configuration changes](17-real-email-delivery-smtp.md#step-175)


---

<a id="step-175"></a>

### Step 175: Restart Django Shell after configuration changes

**What was happening before this step?**

We had changed Django's settings file, but our already-open Django shell might still have been using values it loaded earlier.

**What did we do, and what does it mean?**

A Django shell is an interactive Python window connected to the project. Restarting it makes it read the updated settings afresh. Without restarting, another email test might still use the old terminal-only option and confuse us.

**File or place:** `Django shell (restart)`.

**Code or command from this step**

*1. Copy and paste these lines.*

```python
from django.conf import settings
print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

**Understanding the code, one piece at a time**

The Django shell starts with whatever settings existed when it opened. Exiting and opening a new shell makes it read the updated file. We then print the email settings to see which mode it loaded.

**How did we know what happened?**

The next step sends a test email from the fresh environment.

**Small example:** Changing a recipe on disk does not update a cake already in the oven. Similarly, an already-open Python shell can still hold settings loaded before the edit. Restarting reads them again.

**Where to go next:** Previous: [Step 174 — Remove the conflicting email backend override](17-real-email-delivery-smtp.md#step-174) · Next: [Step 176 — Send a real email through Gmail](17-real-email-delivery-smtp.md#step-176)


---

<a id="step-176"></a>

### Step 176: Send a real email through Gmail

**What was happening before this step?**

After fixing the settings and restarting the shell, we needed to see whether a real message could arrive in Gmail.

**What did we do, and what does it mean?**

We sent a test message with Django's mail function. The function can tell us it handed the message to the chosen sending service, but that alone does not prove it reached the inbox. We also needed to look in the receiving mailbox to confirm delivery.

**File or place:** `Django shell (send_mail)`.

**Code or command from this step**

*1. In the same Django Shell, where you see >>>, paste.*

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

*2. Example result from the notes (not a command to run).*

```text
1
```

*3. Example result from the notes (not a command to run).*

```text
in:anywhere subject:"Techmindz SMTP Test"
```

**Understanding the code, one piece at a time**

The `send_mail` function takes a subject, message text, sender address and recipient list. It returns a count of messages accepted for sending. The count alone is not a receipt from the employee. We still look in the recipient's mailbox.

**How did we know what happened?**

The notes show the send result and subsequent checks. Do not interpret a console backend's `1` as proof of SMTP delivery.

**Small example:** A message count of `1` is not the same as seeing the email in the inbox. We check the selected sending mode and the recipient mailbox too.

**Where to go next:** Previous: [Step 175 — Restart Django Shell after configuration changes](17-real-email-delivery-smtp.md#step-175) · Next: [Step 177 — Plan the shared layout](17-real-email-delivery-smtp.md#step-177)


---

<a id="step-177"></a>

### Step 177: Plan the shared layout

**What was happening before this step?**

Once the email test was working, we turned to the appearance of the account pages. Several pages repeated similar headings, menus and messages.

**What did we do, and what does it mean?**

We reviewed what should be shared across pages. The goal was one common layout, so changing a navigation link later would not mean editing every HTML file separately. The actual shared template begins in the following step.

**File or place:** `template/layout planning`.

**Code or command from this step**

*1. Replace it with.*

```python
if request.user.role not in roles:
    messages.error(request, 'You do not have permission to access this page.')
    return redirect('home')
```

**Understanding the code, one piece at a time**

`redirect(...)` does not display a page itself. It tells the browser which page to open next, often after a form has been saved.

**How did we know what happened?**

This is a transition and review step; it is not evidence that every template was already converted.

**Where to go next:** Previous: [Step 176 — Send a real email through Gmail](17-real-email-delivery-smtp.md#step-176) · Next: [Step 178 — Create the shared base.html template](17-real-email-delivery-smtp.md#step-178)


---

<a id="step-178"></a>

### Step 178: Create the shared base.html template

**What was happening before this step?**

The login, home and employee pages had similar parts, but each page was carrying its own copy. That made them harder to keep consistent.

**What did we do, and what does it mean?**

We created `base.html`, the common starting layout. Other pages can say “use this layout” and provide only their own content. It is like using the same page frame for several different forms.

**File or place:** `templates/base.html`.

**Code or command from this step**

*1. Create this new file in the main templates folder, then paste.*

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

**Understanding the code, one piece at a time**

The common layout contains the HTML structure used by many pages, such as navigation and a place for messages. A named `{% block content %}` is an empty slot each individual page can fill when it extends `base.html`.

**How did we know what happened?**

The individual pages still have to be updated to extend the base template in later steps.

**Where to go next:** Previous: [Step 177 — Plan the shared layout](17-real-email-delivery-smtp.md#step-177) · Next: [Step 179 — Dashboard Template Inheritance](18-shared-base-template-and-page-styling.md#step-179)

---

### The point of this chapter

Imagine the difference between writing a letter on your own desk and posting it to someone. “Console mode” leaves the letter on our desk. “SMTP mode” asks Gmail to deliver it. The chapter follows the mistakes we encountered while switching between those two modes. Steps 177–178 start the next topic: a common design for the website's pages.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

