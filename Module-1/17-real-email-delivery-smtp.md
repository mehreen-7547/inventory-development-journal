# MODULE 1 — Employee accounts

## Chapter 17: Real email delivery (SMTP) (Steps 170–176)

### Before we begin

Until now, an email only appeared as text in our VS Code terminal. That let us examine links, but it could not send a welcome message to a real employee.

### What we worked on

We tried using Gmail to send messages, learned where to keep the private Gmail details, found why Django kept printing messages in the terminal and corrected the settings. We then tested sending again.

### Picture it this way

Imagine the difference between writing a letter on your own desk and posting it to someone. “Console mode” leaves the letter on our desk. “SMTP mode” asks Gmail to deliver it. The chapter follows the mistakes we encountered while switching between those two modes. Steps 177–178 start the next topic: a common design for the website's pages.

### Find a step

- [Step 170: Real Email Configuration](#step-170)
- [Step 171: Configure SMTP Credentials](#step-171)

---

<a id="step-170"></a>

### Step 170: Real Email Configuration

#### Before this step

We had been testing emails by printing their contents in the VS Code terminal. That was useful for development, but a real employee cannot see our terminal. To receive a welcome email, the message must reach their inbox. The preceding step, Step 169, covered message styling css, so this was the next part of the development sequence.

#### What we did and why

We added a choice in Django's settings: keep printing emails during testing, or send them through Gmail. Gmail accepts messages from programs through a service called SMTP. We also told Django where the Gmail address and password would come from, without typing those private values into Python code.

#### Code and commands

**Step 170, block 1.** This code sample or terminal command was used while working on real email configuration. The content below remains in its original sequence for Step 170, block 1.

```python
import os
```

**Step 170, block 2.** This code sample or terminal command was used while working on real email configuration. The content below remains in its original sequence for Step 170, block 2.

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
```

**Step 170, block 3.** This code sample or terminal command was used while working on real email configuration. The content below remains in its original sequence for Step 170, block 3.

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

#### How the code works

**Block 1 (python, 1 lines).** In Step 170, real email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `import os` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 2 lines).** In Step 170, real email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = 'no-reply@example.com'` sets the named value used by the next operation in this file.

**Block 3 (python, 16 lines).** In Step 170, real email configuration involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `EMAIL_MODE = os.getenv('EMAIL_MODE', 'console')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `if EMAIL_MODE == 'smtp':` is a condition: the code inside this branch only runs when its condition is satisfied. `EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `EMAIL_HOST = 'smtp.gmail.com'` sets the named value used by the next operation in this file.

**Lines 8–14:** `EMAIL_HOST_USER = os.getenv('EMAIL_HOST_USER', '')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `EMAIL_HOST_PASSWORD = os.getenv('EMAIL_HOST_PASSWORD', '')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `DEFAULT_FROM_EMAIL = EMAIL_HOST_USER` sets the named value used by the next operation in this file. The `else` branch handles the alternative case when the preceding condition was not satisfied.

**Lines 15–16:** `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = 'no-reply@example.com'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 170. For Step 170, the proposed way to check real email configuration was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 171 before a complete result could be claimed.

#### Next

[Step 169: Message Styling CSS](../Module-1/16-message-styling.md#step-169) · [Step 171: Configure SMTP Credentials](#step-171)


---

<a id="step-171"></a>

### Step 171: Configure SMTP Credentials

#### Before this step

Once Django knew how to send through Gmail, it needed the details of which Gmail account to use. Those details included a special password, so we did not want to put them in a file shared on GitHub. Immediately before this, Step 170 dealt with real email configuration; the work here continued the same part of the application.

#### What we did and why

For this early test, the terminal asked for the Gmail app password and held it only during that terminal session. Django could then read it while running. Closing the terminal would lose those temporary settings, which led to the next step.

#### Code and commands

**Step 171, block 1.** This code sample or terminal command was used while working on configure smtp credentials. The content below remains in its original sequence for Step 171, block 1.

```powershell
$env:EMAIL_MODE = "smtp"

$env:EMAIL_HOST_USER = "YOUR_GMAIL_ADDRESS"

$secure = Read-Host "Enter Gmail App Password" -AsSecureString

$env:EMAIL_HOST_PASSWORD = ([System.Net.NetworkCredential]::new("", $secure).Password) -replace ' ', ''

Remove-Variable secure
```

**Step 171, block 2.** This code sample or terminal command was used while working on configure smtp credentials. The content below remains in its original sequence for Step 171, block 2.

```powershell
python manage.py runserver
```

#### How the code works

**Block 1 (powershell, 9 lines).** In Step 171, configure smtp credentials involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `$env:EMAIL_MODE = "smtp"` sets the named value used by the next operation in this file. `$env:EMAIL_HOST_USER = "YOUR_GMAIL_ADDRESS"` sets the named value used by the next operation in this file. `$secure = Read-Host "Enter Gmail App Password" -AsSecureString` sets the named value used by the next operation in this file. `$env:EMAIL_HOST_PASSWORD = ([System.Net.NetworkCredential]::new("", $secure).Password) -re` sets the named value used by the next operation in this file.

**Lines 8–9:** The lines starting with `Remove-Variable secure` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

**Block 2 (powershell, 1 lines).** In Step 171, configure smtp credentials involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py runserver` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 171. For Step 171, the proposed way to check configure smtp credentials was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 172 before a complete result could be claimed.

#### Next

[Step 170: Real Email Configuration](#step-170) · [Step 172: Install python-dotenv](#step-172)
