# MODULE 1 — Accounts

## Chapter 7: Role-based homepage and feedback messages (Steps 91–99)

**Where we are.** Different staff roles needed clear homepage cues and understandable success/error messages.

**What this chapter gives us.** Distinguish template visibility from real authorization, and connect backend messages to HTML.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why hiding an admin link does not secure the admin URL.

### Find a step

- [Step 91: Role-Based Homepage](#step-91)
- [Step 92: Admin Link Visibility](#step-92)
- [Step 93: Why hiding a link does not secure Django Admin](#step-93)
- [Step 94: Verify manager access is restricted](#step-94)
- [Step 95: Login Error Messages](#step-95)
- [Step 96: Password Change Confirmation](#step-96)
- [Step 97: Success Message](#step-97)
- [Step 98: Stage Files](#step-98)
- [Step 99: Commit Changes](#step-99)

---

<a id="step-91"></a>

### Step 91: Role-Based Homepage

**Why we are doing this.** The homepage needed to reflect whether the signed-in person was Admin, Inventory Manager, or Sales Staff.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Below the welcome message, add.*

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

**How it works.** Template `{% if %}` conditions compare `request.user.role` and show role-specific labels. This improves orientation but is not a security boundary.

**Follow the flow.** `request.user.role` is read from the signed-in user's model row. The template checks it to choose what label to display. This happens while Django renders HTML; it does not prevent a person from directly visiting an unauthorized URL. The reusable role decorator is introduced later for that job.

**How we checked it.** Review the home display under each role; access control is implemented separately in Steps 100–107.

**Connects to.** Previous: [Step 90 — Change Password Link](06-password-change.md#step-90) · Next: [Step 92 — Admin Link Visibility](07-role-based-homepage-and-feedback-messages.md#step-92)


---

<a id="step-92"></a>

### Step 92: Admin Link Visibility

**Why we are doing this.** We had shown the Django Admin link to everyone, even though ordinary staff cannot use the Admin site.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace.*

```html
<p><a href="/admin/">Open Django admin</a></p>
```

*2. Recorded snippet; With.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How it works.** A template condition checks `request.user.is_staff` before rendering the Admin link. This removes a confusing control for non-staff users.

**How we checked it.** The next two entries clarify and verify that Django Admin already rejects unauthorized accounts.

**Connects to.** Previous: [Step 91 — Role-Based Homepage](07-role-based-homepage-and-feedback-messages.md#step-91) · Next: [Step 93 — Why hiding a link does not secure Django Admin](07-role-based-homepage-and-feedback-messages.md#step-93)


---

<a id="step-93"></a>

### Step 93: Why hiding a link does not secure Django Admin

**Why we are doing this.** Hiding a link is cosmetic, so we checked whether manager accounts were actually barred from `/admin/`.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; So what did our code actually change?.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How it works.** Django Admin enforces `is_staff` and appropriate permissions on the server. A user with business role `manager` does not automatically become Django staff.

**How we checked it.** The recorded explanation says `manager1` has `is_staff=False`; a direct URL test is discussed next.

**Connects to.** Previous: [Step 92 — Admin Link Visibility](07-role-based-homepage-and-feedback-messages.md#step-92) · Next: [Step 94 — Verify manager access is restricted](07-role-based-homepage-and-feedback-messages.md#step-94)


---

<a id="step-94"></a>

### Step 94: Verify manager access is restricted

**Why we are doing this.** We confirmed the difference between not seeing an admin link and being unable to access the page.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; One thing remains: your screenshot still shows that link for manager1. Make sure you have saved this change in home.html.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How it works.** Django checks the account's staff privileges even if someone manually opens `/admin/`; the template does not provide that protection.

**How we checked it.** The source describes the manager's denied Admin visit as the expected result.

**Connects to.** Previous: [Step 93 — Why hiding a link does not secure Django Admin](07-role-based-homepage-and-feedback-messages.md#step-93) · Next: [Step 95 — Login Error Messages](07-role-based-homepage-and-feedback-messages.md#step-95)


---

<a id="step-95"></a>

### Step 95: Login Error Messages

**Why we are doing this.** The login view already created an error message for invalid credentials, but the form page did not necessarily show it.

**File / location.** `templates/accounts/login.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Remember this line in accounts/views.py?.*

```python
messages.error(request, 'Invalid username or password.')
```

*2. Recorded snippet; If it isn't already present, add this code just above the login <form>.*

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

**How it works.** The Django messages framework stores short notices during a request. The template loop renders each message so the user knows why login failed.

**Follow the flow.** Calling `messages.error` in the view only stores a notice for display. A template must loop over `messages` to make it visible. When an incorrect login is submitted, this loop shows the error instead of leaving the user wondering why the page reappeared.

**How we checked it.** Try a wrong password and verify an error appears without logging in. No separate automated check was preserved.

**Connects to.** Previous: [Step 94 — Verify manager access is restricted](07-role-based-homepage-and-feedback-messages.md#step-94) · Next: [Step 96 — Password Change Confirmation](07-role-based-homepage-and-feedback-messages.md#step-96)


---

<a id="step-96"></a>

### Step 96: Password Change Confirmation

**Why we are doing this.** A successful password change should give clear feedback after redirecting to the homepage.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find this section inside changepassword().*

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    return redirect('home')
```

*2. Recorded snippet; Add one line before the redirect.*

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    messages.success(request, 'Password changed successfully.')
    return redirect('home')
```

**How it works.** `messages.success` adds a confirmation before `redirect`; `update_session_auth_hash` preserves the logged-in session when the password changes.

Django checks the submitted values before the form is allowed to save data.

The form converts validated input into a database write. On an edit form, the bound instance determines which row is updated.

**How we checked it.** The message is only visible after the homepage template displays messages in Step 97.

**Connects to.** Previous: [Step 95 — Login Error Messages](07-role-based-homepage-and-feedback-messages.md#step-95) · Next: [Step 97 — Success Message](07-role-based-homepage-and-feedback-messages.md#step-97)


---

<a id="step-97"></a>

### Step 97: Success Message

**Why we are doing this.** The homepage needed to render the message that Step 96 now created.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Immediately below the <body> tag, add.*

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

**How it works.** A `{% for message in messages %}` loop reads queued notifications and displays the success text after the redirect.

**How we checked it.** Change a password, return home, and check for the confirmation. This is a suggested functional check, not a stored test log.

**Connects to.** Previous: [Step 96 — Password Change Confirmation](07-role-based-homepage-and-feedback-messages.md#step-96) · Next: [Step 98 — Stage Files](07-role-based-homepage-and-feedback-messages.md#step-98)


---

<a id="step-98"></a>

### Step 98: Stage Files

**Why we are doing this.** The account/authentication work now spanned multiple files, so we reviewed which files belonged in the commit.

**File / location.** `application Git staging`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```bash
git add config/settings.py config/urls.py core/views.py core/templates/core/home.html accounts/ templates/accounts/
```

**How it works.** The targeted `git add` command stages model, view, URL, template, settings, and migration changes while avoiding private local files.

**How we checked it.** The recorded note says twelve changed/new files were appropriate for this module.

**Connects to.** Previous: [Step 97 — Success Message](07-role-based-homepage-and-feedback-messages.md#step-97) · Next: [Step 99 — Commit Changes](07-role-based-homepage-and-feedback-messages.md#step-99)


---

<a id="step-99"></a>

### Step 99: Commit Changes

**Why we are doing this.** After reviewing and staging the working account files, we saved the milestone in the application Git repository.

**File / location.** `application Git commit`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your VS Code terminal.*

```bash
git commit -m "Complete Module 1 accounts and authentication"
```

*2. Recorded example/output — **do not type this as an instruction**; Expected output: Something similar to.*

```text
[main abc1234] Complete Module 1 accounts and authentication
12 files changed, ...
```

**How it works.** `git commit` captures the staged module state locally. A commit records what was written; it does not prove every login scenario was tested.

**How we checked it.** No new runtime feature is introduced by this commit.

**Connects to.** Previous: [Step 98 — Stage Files](07-role-based-homepage-and-feedback-messages.md#step-98) · Next: [Step 100 — Role Security](08-role-security-decorators.md#step-100)

---

**Chapter recap.** Distinguish template visibility from real authorization, and connect backend messages to HTML.

**Practice explaining:** Explain why hiding an admin link does not secure the admin URL.

