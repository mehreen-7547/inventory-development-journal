# MODULE 1 — Accounts

## Chapter 10: Forgot and reset password (Steps 113–127)

**Where we are.** Someone who forgets a password cannot use the logged-in change-password form. Django provides a secure, token-based recovery flow.

**What this chapter gives us.** Link the request form, email, token confirmation, new-password form, and completion page.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Describe the complete password-reset journey without assuming the email itself changes the password.

### Find a step

- [Step 113: Forgot Password](#step-113)
- [Step 114: Password Reset URL](#step-114)
- [Step 115: Password Reset Confirmation](#step-115)
- [Step 116: Understand the password reset templates](#step-116)
- [Step 117: Confirmation Page URL](#step-117)
- [Step 118: Password Reset Email](#step-118)
- [Step 119: Connect Email Template](#step-119)
- [Step 120: Connect Email Subject](#step-120)
- [Step 121: Set New Password Page](#step-121)
- [Step 122: Reset Link URL](#step-122)
- [Step 123: Password Reset Success Page](#step-123)
- [Step 124: Password Reset Complete URL](#step-124)
- [Step 125: Fix the password reset URL error](#step-125)
- [Step 126: Consolidate the accounts URL patterns](#step-126)
- [Step 127: Email Testing](#step-127)

---

<a id="step-113"></a>

### Step 113: Forgot Password

**Why we are doing this.** A user who forgets a password cannot use the signed-in password-change page, so we started a separate recovery flow.

**File / location.** `templates/accounts/password_reset.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Forgot Password</title>
</head>
<body>

    <h2>Forgot Password</h2>

    <p>Enter your registered email address to reset your password.</p>

    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit">Send Reset Link</button>
    </form>

    <a href="{% url 'login' %}">Back to Login</a>

</body>
</html>
```

**How it works.** The forgot-password template asks for the registered email. Django's built-in password reset view handles token generation and response behavior when connected.

The token helps Django reject forged state-changing form submissions.

**How we checked it.** The page cannot yet send a reset request until its URL is added in Step 114.

**Connects to.** Previous: [Step 112 — Email Configuration](09-email-validation-and-email-setup.md#step-112) · Next: [Step 114 — Password Reset URL](10-forgot-and-reset-password.md#step-114)


---

<a id="step-114"></a>

### Step 114: Password Reset URL

**Why we are doing this.** The forgot-password HTML page needed a Django view and a route to process submitted email addresses.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this import at the top.*

```python
from django.contrib.auth import views as auth_views
```

*2. Recorded snippet; Add this entry inside your existing urlpatterns list.*

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html'
    ),
    name='password_reset'
),
```

**How it works.** `PasswordResetView.as_view(...)` creates the configured class-based view; the URL maps the request to it and specifies the form template.

**Follow the flow.** The URL's path is what the browser visits. `PasswordResetView.as_view` turns Django's built-in class-based view into a callable route. The `template_name` option tells it which file contains the email-entry form. Other options added later tell it where to send the email content and which page to show afterwards.

**How we checked it.** The next steps build the confirmation and email templates used by the view.

**Connects to.** Previous: [Step 113 — Forgot Password](10-forgot-and-reset-password.md#step-113) · Next: [Step 115 — Password Reset Confirmation](10-forgot-and-reset-password.md#step-115)


---

<a id="step-115"></a>

### Step 115: Password Reset Confirmation

**Why we are doing this.** After submitting an email, the user needs confirmation that the request was processed, without exposing whether an account exists.

**File / location.** `templates/accounts/password_reset_done.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Password Reset Requested</title>
</head>
<body>

    <h2>Check Your Email</h2>

    <p>
        If an active account exists with that email address,
        password reset instructions will be sent.
    </p>

    <p>
        Please check your email for the reset link.
    </p>

    <a href="{% url 'login' %}">Back to Login</a>

</body>
</html>
```

**How it works.** The confirmation template displays a neutral message. It is rendered by Django's password-reset flow after the form submission.

**How we checked it.** The route to this template is connected in Step 117.

**Connects to.** Previous: [Step 114 — Password Reset URL](10-forgot-and-reset-password.md#step-114) · Next: [Step 116 — Understand the password reset templates](10-forgot-and-reset-password.md#step-116)


---

<a id="step-116"></a>

### Step 116: Understand the password reset templates

**Why we are doing this.** Several templates belong to one reset journey, so we clarified which file corresponds to each screen.

**File / location.** `templates/accounts/ (reference)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; In accounts/urls.py, this path.*

```python
template_name='accounts/password_reset.html'
```

**How it works.** The reset form, request-confirmation page, email message, new-password form, and final success page are separate templates with separate responsibilities.

**How we checked it.** This organizational step does not introduce another view.

**Connects to.** Previous: [Step 115 — Password Reset Confirmation](10-forgot-and-reset-password.md#step-115) · Next: [Step 117 — Confirmation Page URL](10-forgot-and-reset-password.md#step-117)


---

<a id="step-117"></a>

### Step 117: Confirmation Page URL

**Why we are doing this.** Django needed to know where to redirect after handling the forgot-password request.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside your existing urlpatterns list.*

```python
path(
    'password-reset/done/',
    auth_views.PasswordResetDoneView.as_view(
        template_name='accounts/password_reset_done.html'
    ),
    name='password_reset_done'
),
```

**How it works.** The confirmation URL connects `PasswordResetDoneView` to the correct template; the initial reset view references its named success destination.

**How we checked it.** Test the flow after the email and password confirmation routes are finished.

**Connects to.** Previous: [Step 116 — Understand the password reset templates](10-forgot-and-reset-password.md#step-116) · Next: [Step 118 — Password Reset Email](10-forgot-and-reset-password.md#step-118)


---

<a id="step-118"></a>

### Step 118: Password Reset Email

**Why we are doing this.** An employee cannot reset a password unless the email explains where to go and contains the secure reset link.

**File / location.** `templates/accounts/password_reset_email.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```django
Hello,

We received a request to reset your Inventory System password.

Click the link below to set a new password:

{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}

If you did not request this password reset, you can ignore this email.

Inventory System
```

*2. Recorded snippet; Understanding the important line.*

```django
{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}
```

**How it works.** The email template includes a URL with a user identifier and token supplied by Django. The token is not a password and should never be hardcoded.

**Follow the flow.** Django supplies the message template with a user identifier and a signed reset token. The template builds a URL from these values. The email link does not directly set a new password; it sends the recipient to a page where Django checks the token before showing a form.

**How we checked it.** The reset view still needs to reference this template in the next step.

**Connects to.** Previous: [Step 117 — Confirmation Page URL](10-forgot-and-reset-password.md#step-117) · Next: [Step 119 — Connect Email Template](10-forgot-and-reset-password.md#step-119)


---

<a id="step-119"></a>

### Step 119: Connect Email Template

**Why we are doing this.** Creating the email template file was not sufficient; Django had to be told to use it.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find your existing PasswordResetView and update it to.*

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html',
        email_template_name='accounts/password_reset_email.txt'
    ),
    name='password_reset'
),
```

*2. Recorded snippet; We added one line.*

```python
email_template_name='accounts/password_reset_email.txt'
```

**How it works.** The `email_template_name` option on `PasswordResetView` points to the body template for outgoing messages.

**How we checked it.** The next step configures the message subject as well.

**Connects to.** Previous: [Step 118 — Password Reset Email](10-forgot-and-reset-password.md#step-118) · Next: [Step 120 — Connect Email Subject](10-forgot-and-reset-password.md#step-120)


---

<a id="step-120"></a>

### Step 120: Connect Email Subject

**Why we are doing this.** A reset email should have a recognizable subject so the recipient understands what the message is for.

**File / location.** `accounts/urls.py and subject template`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find your existing PasswordResetView and update it to.*

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html',
        email_template_name='accounts/password_reset_email.txt',
        subject_template_name='accounts/password_reset_subject.txt'
    ),
    name='password_reset'
),
```

*2. Recorded snippet; We added.*

```python
subject_template_name='accounts/password_reset_subject.txt'
```

**How it works.** The view's subject-template setting uses a separate text template for the email subject. This keeps email content editable without rewriting the view.

**How we checked it.** The source records the configuration but no delivered-email test yet.

**Connects to.** Previous: [Step 119 — Connect Email Template](10-forgot-and-reset-password.md#step-119) · Next: [Step 121 — Set New Password Page](10-forgot-and-reset-password.md#step-121)


---

<a id="step-121"></a>

### Step 121: Set New Password Page

**Why we are doing this.** After clicking the reset link, the recipient needs a page that accepts and validates a new password.

**File / location.** `templates/accounts/password_reset_confirm.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Set New Password</title>
</head>
<body>

    {% if validlink %}

        <h2>Set New Password</h2>

        <form method="POST">
            {% csrf_token %}

            {{ form.as_p }}

            <button type="submit">Reset Password</button>
        </form>

    {% else %}

        <h2>Invalid Reset Link</h2>

        <p>
            This password reset link is invalid or has expired.
            Please request a new one.
        </p>

        <a href="{% url 'password_reset' %}">
            Request New Link
        </a>

    {% endif %}

</body>
</html>
```

**How it works.** The confirmation template renders Django's password-setting form. The view validates token and password rules before saving.

The token helps Django reject forged state-changing form submissions.

**How we checked it.** The URL that receives token-bearing links is added next.

**Connects to.** Previous: [Step 120 — Connect Email Subject](10-forgot-and-reset-password.md#step-120) · Next: [Step 122 — Reset Link URL](10-forgot-and-reset-password.md#step-122)


---

<a id="step-122"></a>

### Step 122: Reset Link URL

**Why we are doing this.** The link in the email contains a user identifier and token, so Django needs a route with corresponding URL parameters.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside your existing urlpatterns.*

```python
path(
    'password-reset/confirm/<uidb64>/<token>/',
    auth_views.PasswordResetConfirmView.as_view(
        template_name='accounts/password_reset_confirm.html'
    ),
    name='password_reset_confirm'
),
```

**How it works.** `PasswordResetConfirmView` checks the token and displays the new-password form if it is valid. The URL pattern names the token-bearing path.

**Follow the flow.** The `<uidb64>` and `<token>` pieces in the URL are captured from the link as variables. Django's `PasswordResetConfirmView` checks them against the relevant account and the configured timeout. A valid link can open the new-password form; an invalid link should not permit a change.

**How we checked it.** This should be tested using an email link, not a manually invented token.

**Connects to.** Previous: [Step 121 — Set New Password Page](10-forgot-and-reset-password.md#step-121) · Next: [Step 123 — Password Reset Success Page](10-forgot-and-reset-password.md#step-123)


---

<a id="step-123"></a>

### Step 123: Password Reset Success Page

**Why we are doing this.** Once a new password is saved, the user needs a clear endpoint showing the recovery workflow is complete.

**File / location.** `templates/accounts/password_reset_complete.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Password Reset Complete</title>
</head>
<body>

    <h2>Password Reset Successful</h2>

    <p>
        Your password has been reset successfully.
        You can now log in using your new password.
    </p>

    <a href="{% url 'login' %}">Go to Login</a>

</body>
</html>
```

**How it works.** The success template confirms the reset and provides a way back to sign-in. It does not itself change the password; the confirmation view performs that action.

**How we checked it.** The route to this final screen is connected in Step 124.

**Connects to.** Previous: [Step 122 — Reset Link URL](10-forgot-and-reset-password.md#step-122) · Next: [Step 124 — Password Reset Complete URL](10-forgot-and-reset-password.md#step-124)


---

<a id="step-124"></a>

### Step 124: Password Reset Complete URL

**Why we are doing this.** The reset-confirm view needs a configured success destination after accepting a new password.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside your existing urlpatterns.*

```python
path(
    'password-reset/complete/',
    auth_views.PasswordResetCompleteView.as_view(
        template_name='accounts/password_reset_complete.html'
    ),
    name='password_reset_complete'
),
```

**How it works.** `PasswordResetCompleteView` maps the final URL to the completion template, closing the password recovery sequence.

**How we checked it.** At this point the intended end-to-end route exists, subject to fixing the URL issue found next.

**Connects to.** Previous: [Step 123 — Password Reset Success Page](10-forgot-and-reset-password.md#step-123) · Next: [Step 125 — Fix the password reset URL error](10-forgot-and-reset-password.md#step-125)


---

<a id="step-125"></a>

### Step 125: Fix the password reset URL error

**Why we are doing this.** A Django error showed that one part of the password-reset URL configuration was inconsistent.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded example/output — **do not type this as an instruction**; Your terminal shows.*

```text
TypeError: PasswordResetDoneView() received an invalid
keyword 'email_template_name'.
```

*2. Recorded snippet.*

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html',
        email_template_name='accounts/password_reset_email.txt',
        subject_template_name='accounts/password_reset_subject.txt'
    ),
    name='password_reset'
),

path(
    'password-reset/done/',
    auth_views.PasswordResetDoneView.as_view(
        template_name='accounts/password_reset_done.html'
    ),
    name='password_reset_done'
),
```

*3. Recorded snippet; Save the file and run.*

```powershell
python manage.py runserver
```

*4. Recorded example/output — **do not type this as an instruction**.*

```text
System check identified no issues (0 silenced).
Starting development server at http://127.0.0.1:8000/
```

**How it works.** The notes diagnose and correct the relationship between route names, view configuration, and templates. A name mismatch can cause `NoReverseMatch` even when individual templates exist.

**Follow the flow.** The error was caused by passing an email-related option to `PasswordResetDoneView`, which only displays the confirmation page. The email template and subject options belong to `PasswordResetView`, the component that initiates sending. Moving them to the correct view resolves the responsibility mismatch.

**How we checked it.** The error prompted a revised URL configuration in Step 126.

**Connects to.** Previous: [Step 124 — Password Reset Complete URL](10-forgot-and-reset-password.md#step-124) · Next: [Step 126 — Consolidate the accounts URL patterns](10-forgot-and-reset-password.md#step-126)


---

<a id="step-126"></a>

### Step 126: Consolidate the accounts URL patterns

**Why we are doing this.** To remove partial or conflicting reset routes, we consolidated the accounts URL patterns in one file.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents of accounts/urls.py with this corrected version.*

```python
from django.urls import path
from . import views
from django.contrib.auth import views as auth_views

urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
    path('change-password/', views.changepassword, name='change_password'),

    path(
        'password-reset/',
        auth_views.PasswordResetView.as_view(
            template_name='accounts/password_reset.html',
            email_template_name='accounts/password_reset_email.txt',
            subject_template_name='accounts/password_reset_subject.txt'
        ),
        name='password_reset'
    ),

    path(
        'password-reset/done/',
        auth_views.PasswordResetDoneView.as_view(
            template_name='accounts/password_reset_done.html'
        ),
        name='password_reset_done'
    ),

    path(
        'password-reset/confirm/<uidb64>/<token>/',
        auth_views.PasswordResetConfirmView.as_view(
            template_name='accounts/password_reset_confirm.html'
        ),
        name='password_reset_confirm'
    ),

    path(
        'password-reset/complete/',
        auth_views.PasswordResetCompleteView.as_view(
            template_name='accounts/password_reset_complete.html'
        ),
        name='password_reset_complete'
    ),
]
```

**How it works.** The full `accounts/urls.py` includes login, logout, password change, reset form, reset email confirmation, token link, and completion routes.

**How we checked it.** The following step tests email output using the now-connected flow.

**Connects to.** Previous: [Step 125 — Fix the password reset URL error](10-forgot-and-reset-password.md#step-125) · Next: [Step 127 — Email Testing](10-forgot-and-reset-password.md#step-127)


---

<a id="step-127"></a>

### Step 127: Email Testing

**Why we are doing this.** We wanted proof that entering a registered email triggered the recovery flow without relying on a paid mail provider.

**File / location.** `Django terminal email backend`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

**How it works.** With the console backend, the password-reset email should appear in the terminal. The link can then be opened to test token-based password replacement.

This setting selects whether Django prints email locally or tries to deliver it through SMTP.

**How we checked it.** The notes describe email testing; they do not prove real SMTP delivery at this stage.

**Connects to.** Previous: [Step 126 — Consolidate the accounts URL patterns](10-forgot-and-reset-password.md#step-126) · Next: [Step 128 — Employee Creation Form](11-employee-welcome-email-and-password-setup-link.md#step-128)

---

**Chapter recap.** Link the request form, email, token confirmation, new-password form, and completion page.

**Practice explaining:** Describe the complete password-reset journey without assuming the email itself changes the password.

