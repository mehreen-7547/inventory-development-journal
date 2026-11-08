# MODULE 1 — Employee accounts

## Chapter 10: Forgot and reset password (Steps 113–127)

### Before we begin

We already had a way to change a password while signed in. But someone who forgot their password could not use it.

### What we worked on

We built the full forgotten-password journey: request a link, display a confirmation, send a message, open the link, choose a new password and return to login.

### Picture it this way

There are several small pages because each has a different job. Django's built-in reset views handle token checks; our templates decide what the person sees.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

A signed-in person can change a known password, but someone who has forgotten it cannot use that screen.

**What did we do, and what does it mean?**

We started a separate “Forgot password?” page where a user can enter an email address. Django then handles the reset process without requiring the old password.

**File or place:** `templates/accounts/password_reset.html`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

The page cannot yet send a reset request until its URL is added in Step 114.

**Where to go next:** Previous: [Step 112 — Email Configuration](09-email-validation-and-email-setup.md#step-112) · Next: [Step 114 — Password Reset URL](10-forgot-and-reset-password.md#step-114)


---

<a id="step-114"></a>

### Step 114: Password Reset URL

**What was happening before this step?**

The forgot-password form needed a web address and a Django view to handle its submission.

**What did we do, and what does it mean?**

Django provides a ready-made password-reset view. We connected it to our template, so the form can ask Django to prepare a reset email when submitted.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this import at the top.*

```python
from django.contrib.auth import views as auth_views
```

*2. Add this entry inside your existing urlpatterns list.*

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html'
    ),
    name='password_reset'
),
```

**Understanding the code, one piece at a time**

Django's built-in `PasswordResetView` does the work of receiving the email address and preparing the reset. Our URL rule tells the browser where to reach it, and the template decides which form is displayed.

**How did we know what happened?**

The next steps build the confirmation and email templates used by the view.

**Where to go next:** Previous: [Step 113 — Forgot Password](10-forgot-and-reset-password.md#step-113) · Next: [Step 115 — Password Reset Confirmation](10-forgot-and-reset-password.md#step-115)


---

<a id="step-115"></a>

### Step 115: Password Reset Confirmation

**What was happening before this step?**

After entering an email, a person should know the request has been accepted without being told whether the address belongs to an account.

**What did we do, and what does it mean?**

The confirmation page gives a neutral message. This helps people understand the next step while avoiding information that could help someone guess which employees are registered.

**File or place:** `templates/accounts/password_reset_done.html`.

**Code or command from this step**

*1. Paste.*

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

**How did we know what happened?**

The route to this template is connected in Step 117.

**Where to go next:** Previous: [Step 114 — Password Reset URL](10-forgot-and-reset-password.md#step-114) · Next: [Step 116 — Understand the password reset templates](10-forgot-and-reset-password.md#step-116)


---

<a id="step-116"></a>

### Step 116: Understand the password reset templates

**What was happening before this step?**

The password-reset journey uses more than one screen, and their filenames were getting confusing.

**What did we do, and what does it mean?**

We mapped the journey in order: enter email, see confirmation, open the link, set a new password, then see completion. Each template has one place in that journey.

**File or place:** `templates/accounts/ (reference)`.

**Code or command from this step**

*1. In accounts/urls.py, this path.*

```python
template_name='accounts/password_reset.html'
```

**How did we know what happened?**

This organizational step does not introduce another view.

**Where to go next:** Previous: [Step 115 — Password Reset Confirmation](10-forgot-and-reset-password.md#step-115) · Next: [Step 117 — Confirmation Page URL](10-forgot-and-reset-password.md#step-117)


---

<a id="step-117"></a>

### Step 117: Confirmation Page URL

**What was happening before this step?**

The password-reset view needed to know which page to show after the email request.

**What did we do, and what does it mean?**

We connected the confirmation URL as the next destination. Otherwise users could submit the form but end up on the wrong page or encounter a missing link.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside your existing urlpatterns list.*

```python
path(
    'password-reset/done/',
    auth_views.PasswordResetDoneView.as_view(
        template_name='accounts/password_reset_done.html'
    ),
    name='password_reset_done'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

Test the flow after the email and password confirmation routes are finished.

**Where to go next:** Previous: [Step 116 — Understand the password reset templates](10-forgot-and-reset-password.md#step-116) · Next: [Step 118 — Password Reset Email](10-forgot-and-reset-password.md#step-118)


---

<a id="step-118"></a>

### Step 118: Password Reset Email

**What was happening before this step?**

The email had to tell the user where to set a new password.

**What did we do, and what does it mean?**

The email template builds a link containing information Django can later check. It does not contain the person's password and it does not change the password just by being opened.

**File or place:** `templates/accounts/password_reset_email.html`.

**Code or command from this step**

*1. Paste.*

```django
Hello,

We received a request to reset your Inventory System password.

Click the link below to set a new password:

{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}

If you did not request this password reset, you can ignore this email.

Inventory System
```

*2. Understanding the important line.*

```django
{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}
```

**Understanding the code, one piece at a time**

The reset email is not a password. It contains a link with temporary information Django can check. Opening the link sends the recipient to Django's password-setting page; it does not reveal the old password.

**How did we know what happened?**

The reset view still needs to reference this template in the next step.

**Where to go next:** Previous: [Step 117 — Confirmation Page URL](10-forgot-and-reset-password.md#step-117) · Next: [Step 119 — Connect Email Template](10-forgot-and-reset-password.md#step-119)


---

<a id="step-119"></a>

### Step 119: Connect Email Template

**What was happening before this step?**

We created the email text, but Django would not use it unless we explicitly pointed the reset view to it.

**What did we do, and what does it mean?**

The URL configuration names the email template. When Django prepares a reset message, it fills that template with the appropriate reset link.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Find your existing PasswordResetView and update it to.*

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

*2. We added one line.*

```python
email_template_name='accounts/password_reset_email.txt'
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The next step configures the message subject as well.

**Where to go next:** Previous: [Step 118 — Password Reset Email](10-forgot-and-reset-password.md#step-118) · Next: [Step 120 — Connect Email Subject](10-forgot-and-reset-password.md#step-120)


---

<a id="step-120"></a>

### Step 120: Connect Email Subject

**What was happening before this step?**

The reset message also needed a clear subject line so a recipient could recognise it.

**What did we do, and what does it mean?**

The subject is a small text template used as the email's title. This change helps people understand why they received the message; it does not change reset-link security.

**File or place:** `accounts/urls.py and subject template`.

**Code or command from this step**

*1. Find your existing PasswordResetView and update it to.*

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

*2. We added.*

```python
subject_template_name='accounts/password_reset_subject.txt'
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The notes record the configuration but no delivered-email test yet.

**Where to go next:** Previous: [Step 119 — Connect Email Template](10-forgot-and-reset-password.md#step-119) · Next: [Step 121 — Set New Password Page](10-forgot-and-reset-password.md#step-121)


---

<a id="step-121"></a>

### Step 121: Set New Password Page

**What was happening before this step?**

The recipient clicked a reset link. We now needed the screen where the replacement password could be entered.

**What did we do, and what does it mean?**

The confirmation template displays Django's new-password form. Django checks whether the link is valid before allowing the actual password update.

**File or place:** `templates/accounts/password_reset_confirm.html`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

The URL that receives token-bearing links is added next.

**Where to go next:** Previous: [Step 120 — Connect Email Subject](10-forgot-and-reset-password.md#step-120) · Next: [Step 122 — Reset Link URL](10-forgot-and-reset-password.md#step-122)


---

<a id="step-122"></a>

### Step 122: Reset Link URL

**What was happening before this step?**

The reset URL looks unusual because it carries a user identifier and a temporary token.

**What did we do, and what does it mean?**

The identifier tells Django which account the link is for. The token is a check that Django generates and validates. If the link is wrong or too old, it should not let someone set a password.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside your existing urlpatterns.*

```python
path(
    'password-reset/confirm/<uidb64>/<token>/',
    auth_views.PasswordResetConfirmView.as_view(
        template_name='accounts/password_reset_confirm.html'
    ),
    name='password_reset_confirm'
),
```

**Understanding the code, one piece at a time**

The identifier in the link points to an account, and the token helps Django decide whether the link is allowed. A token that is invalid or too old should not let someone set a new password.

**How did we know what happened?**

This should be tested using an email link, not a manually invented token.

**Where to go next:** Previous: [Step 121 — Set New Password Page](10-forgot-and-reset-password.md#step-121) · Next: [Step 123 — Password Reset Success Page](10-forgot-and-reset-password.md#step-123)


---

<a id="step-123"></a>

### Step 123: Password Reset Success Page

**What was happening before this step?**

After changing the password, the person needed a clear “finished” screen.

**What did we do, and what does it mean?**

The completion template tells them the reset has succeeded and normally gives a way back to login. It does not repeat the password or reveal it on screen.

**File or place:** `templates/accounts/password_reset_complete.html`.

**Code or command from this step**

*1. Paste.*

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

**How did we know what happened?**

The route to this final screen is connected in Step 124.

**Where to go next:** Previous: [Step 122 — Reset Link URL](10-forgot-and-reset-password.md#step-122) · Next: [Step 124 — Password Reset Complete URL](10-forgot-and-reset-password.md#step-124)


---

<a id="step-124"></a>

### Step 124: Password Reset Complete URL

**What was happening before this step?**

The reset-confirmation view needed to know where to go after a valid new password had been saved.

**What did we do, and what does it mean?**

Its success destination points to the completion page. This completes the chain from the original email request to the final screen.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside your existing urlpatterns.*

```python
path(
    'password-reset/complete/',
    auth_views.PasswordResetCompleteView.as_view(
        template_name='accounts/password_reset_complete.html'
    ),
    name='password_reset_complete'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

At this point the intended end-to-end route exists, subject to fixing the URL issue found next.

**Where to go next:** Previous: [Step 123 — Password Reset Success Page](10-forgot-and-reset-password.md#step-123) · Next: [Step 125 — Fix the password reset URL error](10-forgot-and-reset-password.md#step-125)


---

<a id="step-125"></a>

### Step 125: Fix the password reset URL error

**What was happening before this step?**

A Django error showed that an option had been given to the wrong part of the reset process.

**What did we do, and what does it mean?**

The view that sends emails needs email-template settings. The view that merely displays “check your inbox” does not. We moved the option to the part that actually uses it, instead of changing the whole workflow.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Example result from the notes (not a command to run).*

```text
TypeError: PasswordResetDoneView() received an invalid
keyword 'email_template_name'.
```

*2. Code or command used at this point.*

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

*3. Save the file and run.*

```powershell
python manage.py runserver
```

*4. Example result from the notes (not a command to run).*

```text
System check identified no issues (0 silenced).
Starting development server at http://127.0.0.1:8000/
```

**Understanding the code, one piece at a time**

`PasswordResetView` is for requesting and sending a reset email. `PasswordResetDoneView` is just the confirmation page shown after the request. It does not use email-template settings, which is why the earlier placement caused an error.

**How did we know what happened?**

The error prompted a revised URL configuration in Step 126.

**Where to go next:** Previous: [Step 124 — Password Reset Complete URL](10-forgot-and-reset-password.md#step-124) · Next: [Step 126 — Consolidate the accounts URL patterns](10-forgot-and-reset-password.md#step-126)


---

<a id="step-126"></a>

### Step 126: Consolidate the accounts URL patterns

**What was happening before this step?**

After several edits, the accounts URL file contained pieces of the reset flow that were hard to follow.

**What did we do, and what does it mean?**

We brought the URL rules together in one orderly file. That makes it easier to see each address and the view that handles it and reduces the chance of conflicting routes.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Replace the entire contents of accounts/urls.py with this corrected version.*

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

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The following step tests email output using the now-connected flow.

**Where to go next:** Previous: [Step 125 — Fix the password reset URL error](10-forgot-and-reset-password.md#step-125) · Next: [Step 127 — Email Testing](10-forgot-and-reset-password.md#step-127)


---

<a id="step-127"></a>

### Step 127: Email Testing

**What was happening before this step?**

We wanted to know whether the reset flow actually generated an email for a registered account.

**What did we do, and what does it mean?**

In console mode the email appears in the terminal rather than someone's inbox. The useful checks were that the form accepted the request, the message appeared and the link led to the expected reset screen.

**File or place:** `Django terminal email backend`.

**Code or command from this step**

*1. Code or command used at this point.*

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

**How did we know what happened?**

The notes describe email testing; they do not prove real SMTP delivery at this stage.

**Where to go next:** Previous: [Step 126 — Consolidate the accounts URL patterns](10-forgot-and-reset-password.md#step-126) · Next: [Step 128 — Employee Creation Form](11-employee-welcome-email-and-password-setup-link.md#step-128)

---

### The point of this chapter

There are several small pages because each has a different job. Django's built-in reset views handle token checks; our templates decide what the person sees.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

