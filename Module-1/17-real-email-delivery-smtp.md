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
- [Step 172: Install python-dotenv](#step-172)
- [Step 173: Fix Email Backend](#step-173)
- [Step 174: Remove the conflicting email backend override](#step-174)

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


---

<a id="step-172"></a>

### Step 172: Install python-dotenv

#### Before this step

Typing the Gmail details again every time was inconvenient. We needed somewhere on this computer to keep them without uploading them to GitHub. Immediately before this, Step 171 dealt with configure smtp credentials; the work here continued the same part of the application.

#### What we did and why

We used a file named .env, which is just a small private settings file containing lines like EMAIL_MODE=smtp. The python-dotenv package reads that file when Django starts. This only works if Django loads the file before it asks what email mode to use.

#### Code and commands

**Step 172, block 1.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 1.

```powershell
python -m pip install python-dotenv
```

**Step 172, block 2.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 2.

```dotenv
EMAIL_MODE=smtp
EMAIL_HOST_USER=YOUR_GMAIL_ADDRESS
EMAIL_HOST_PASSWORD=YOUR_16_CHARACTER_APP_PASSWORD
```

**Step 172, block 3.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 3.

```python
from dotenv import load_dotenv
```

**Step 172, block 4.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 4.

```python
BASE_DIR = Path(__file__).resolve().parent.parent
```

**Step 172, block 5.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 5.

```python
load_dotenv(BASE_DIR / '.env')
```

**Step 172, block 6.** This code sample or terminal command was used while working on install python-dotenv. The content below remains in its original sequence for Step 172, block 6.

```gitignore
.env
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -m pip install python-dotenv` starts Python or one of its tools from the development terminal.

**Block 2 (dotenv, 3 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `EMAIL_MODE=smtp` sets the named value used by the next operation in this file. `EMAIL_HOST_USER=YOUR_GMAIL_ADDRESS` sets the named value used by the next operation in this file. `EMAIL_HOST_PASSWORD=YOUR_16_CHARACTER_APP_PASSWORD` sets the named value used by the next operation in this file.

**Block 3 (python, 1 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from dotenv import load_dotenv` makes a Python function, class or module available to the code that follows.

**Block 4 (python, 1 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `BASE_DIR = Path(__file__).resolve().parent.parent` sets the named value used by the next operation in this file.

**Block 5 (python, 1 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `load_dotenv(BASE_DIR / '.env')` loads settings from the local `.env` file before the application reads them.

**Block 6 (gitignore, 1 lines).** In Step 172, install python-dotenv involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.env` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 172. For Step 172, the proposed way to check install python-dotenv was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 173 before a complete result could be claimed.

#### Next

[Step 171: Configure SMTP Credentials](#step-171) · [Step 173: Fix Email Backend](#step-173)


---

<a id="step-173"></a>

### Step 173: Fix Email Backend

#### Before this step

We tried sending an email, but the message still appeared in the terminal. That told us Django was behaving as if testing mode were still selected. Immediately before this, Step 172 dealt with install python-dotenv; the work here continued the same part of the application.

#### What we did and why

We checked what email mode Django was actually using, instead of assuming the new settings worked. The investigation pointed to when .env was loaded. If the settings are read before that file is loaded, Django cannot see the Gmail details and may use the default terminal mode.

#### Code and commands

**Step 173, block 1.** This output was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 1.

```text
From: no-reply@example.com
```

**Step 173, block 2.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 2.

```python
import os
from dotenv import load_dotenv
```

**Step 173, block 3.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 3.

```python
load_dotenv(BASE_DIR / '.env')
```

**Step 173, block 4.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 4.

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

**Step 173, block 5.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 5.

```python
exit()
```

**Step 173, block 6.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 6.

```powershell
python manage.py shell
```

**Step 173, block 7.** This code sample or terminal command was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 7.

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

**Step 173, block 8.** This output was used while working on fix email backend. The content below remains in its original sequence for Step 173, block 8.

```text
smtp
django.core.mail.backends.smtp.EmailBackend
YOUR_GMAIL_ADDRESS
```

#### How the code works

**Block 1 (text, 1 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 173, output block 1 showed the result rather than another command. Its visible value was `From: no-reply@example.com`, which mattered for fix email backend.

**Block 2 (python, 2 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `import os` makes a Python function, class or module available to the code that follows. `from dotenv import load_dotenv` makes a Python function, class or module available to the code that follows.

**Block 3 (python, 1 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `load_dotenv(BASE_DIR / '.env')` loads settings from the local `.env` file before the application reads them.

**Block 4 (python, 16 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `EMAIL_MODE = os.getenv('EMAIL_MODE', 'console')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `if EMAIL_MODE == 'smtp':` is a condition: the code inside this branch only runs when its condition is satisfied. `EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `EMAIL_HOST = 'smtp.gmail.com'` sets the named value used by the next operation in this file.

**Lines 8–14:** `EMAIL_HOST_USER = os.getenv('EMAIL_HOST_USER', '')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `EMAIL_HOST_PASSWORD = os.getenv('EMAIL_HOST_PASSWORD', '')` reads a configuration value from the running environment instead of putting the private value directly into Python source. `DEFAULT_FROM_EMAIL = EMAIL_HOST_USER` sets the named value used by the next operation in this file. The `else` branch handles the alternative case when the preceding condition was not satisfied.

**Lines 15–16:** `EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = 'no-reply@example.com'` sets the named value used by the next operation in this file.

**Block 5 (python, 1 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `exit()` supply the values or page content for this part of config/settings.py. These lines are kept together because their order and indentation matter.

**Block 6 (powershell, 1 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py shell` starts Python or one of its tools from the development terminal.

**Block 7 (python, 5 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `from django.conf import settings` makes a Python function, class or module available to the code that follows. `print(settings.EMAIL_MODE)` reads one of the Django configuration values used by this feature. `print(settings.EMAIL_BACKEND)` selects whether email is printed for development or handed to an actual mail server. `print(settings.DEFAULT_FROM_EMAIL)` reads one of the Django configuration values used by this feature.

**Block 8 (text, 3 lines).** In Step 173, fix email backend involved `config/settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 173, output block 8 showed the result rather than another command. Its visible value was `smtp`, which mattered for fix email backend.

#### How we checked it

For Step 173 (Fix Email Backend), we compared the visible Django response or result with the requested action. The visible result for Step 173 was described as “your output confirms”; this only addressed the fix email backend check in `config/settings.py`.

#### Next

[Step 172: Install python-dotenv](#step-172) · [Step 174: Remove the conflicting email backend override](#step-174)


---

<a id="step-174"></a>

### Step 174: Remove the conflicting email backend override

#### Before this step

Even after dealing with the private settings file, the email still used the wrong mode. We found another cause in settings.py. Immediately before this, Step 173 dealt with fix email backend; the work here continued the same part of the application.

#### What we did and why

The file was setting the email option twice: first in the new “Gmail or terminal” choice, then later again to “terminal.” Python keeps the last value it sees, so the later line cancelled our Gmail setting. Removing that extra line allowed the original choice to work.

#### Code and commands

**Step 174, block 1.** This code sample or terminal command was used while working on remove the conflicting email backend override. The content below remains in its original sequence for Step 174, block 1.

```python
EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"
DEFAULT_FROM_EMAIL = 'no-reply@example.com'
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

**Step 174, block 2.** This code sample or terminal command was used while working on remove the conflicting email backend override. The content below remains in its original sequence for Step 174, block 2.

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

**Step 174, block 3.** This code sample or terminal command was used while working on remove the conflicting email backend override. The content below remains in its original sequence for Step 174, block 3.

```dotenv
EMAIL_MODE=smtp
EMAIL_HOST_USER=YOUR_GMAIL_ADDRESS
EMAIL_HOST_PASSWORD=YOUR_GMAIL_APP_PASSWORD
```

**Step 174, block 4.** This code sample or terminal command was used while working on remove the conflicting email backend override. The content below remains in its original sequence for Step 174, block 4.

```python
from django.conf import settings

print(settings.EMAIL_MODE)
print(settings.EMAIL_BACKEND)
print(settings.DEFAULT_FROM_EMAIL)
```

**Step 174, block 5.** This output was used while working on remove the conflicting email backend override. The content below remains in its original sequence for Step 174, block 5.

```text
smtp
django.core.mail.backends.smtp.EmailBackend
YOUR_GMAIL_ADDRESS
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 174, remove the conflicting email backend override involved `settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = 'no-reply@example.com'` sets the named value used by the next operation in this file. `PASSWORD_RESET_TIMEOUT = 60 * 60 * 24` sets the named value used by the next operation in this file.

**Block 2 (python, 18 lines).** In Step 174, remove the conflicting email backend override involved `settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The comment `# Email configuration` explains the intention of the following section; it does not execute as application logic. `EMAIL_MODE = os.getenv("EMAIL_MODE", "console").strip().lower()` reads a configuration value from the running environment instead of putting the private value directly into Python source. `if EMAIL_MODE == "smtp":` is a condition: the code inside this branch only runs when its condition is satisfied. `EMAIL_BACKEND = "django.core.mail.backends.smtp.EmailBackend"` selects whether email is printed for development or handed to an actual mail server.

**Lines 8–14:** `EMAIL_USE_TLS = True` sets the named value used by the next operation in this file. `EMAIL_HOST_USER = os.getenv("EMAIL_HOST_USER", "")` reads a configuration value from the running environment instead of putting the private value directly into Python source. `EMAIL_HOST_PASSWORD = os.getenv("EMAIL_HOST_PASSWORD", "")` reads a configuration value from the running environment instead of putting the private value directly into Python source. `DEFAULT_FROM_EMAIL = EMAIL_HOST_USER` sets the named value used by the next operation in this file.

**Lines 15–18:** `EMAIL_BACKEND = "django.core.mail.backends.console.EmailBackend"` selects whether email is printed for development or handed to an actual mail server. `DEFAULT_FROM_EMAIL = "no-reply@example.com"` sets the named value used by the next operation in this file. `PASSWORD_RESET_TIMEOUT = 60 * 60 * 24` sets the named value used by the next operation in this file.

**Block 3 (dotenv, 3 lines).** In Step 174, remove the conflicting email backend override involved `settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `EMAIL_MODE=smtp` sets the named value used by the next operation in this file. `EMAIL_HOST_USER=YOUR_GMAIL_ADDRESS` sets the named value used by the next operation in this file. `EMAIL_HOST_PASSWORD=YOUR_GMAIL_APP_PASSWORD` sets the named value used by the next operation in this file.

**Block 4 (python, 5 lines).** In Step 174, remove the conflicting email backend override involved `settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `from django.conf import settings` makes a Python function, class or module available to the code that follows. `print(settings.EMAIL_MODE)` reads one of the Django configuration values used by this feature. `print(settings.EMAIL_BACKEND)` selects whether email is printed for development or handed to an actual mail server. `print(settings.DEFAULT_FROM_EMAIL)` reads one of the Django configuration values used by this feature.

**Block 5 (text, 3 lines).** In Step 174, remove the conflicting email backend override involved `settings.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 174, output block 5 showed the result rather than another command. Its visible value was `smtp`, which mattered for remove the conflicting email backend override.

#### How we checked it

Not tested yet at Step 174. For Step 174, the proposed way to check remove the conflicting email backend override was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 175 before a complete result could be claimed.

#### Next

[Step 173: Fix Email Backend](#step-173) · [Step 175: Restart Django Shell after configuration changes](#step-175)
