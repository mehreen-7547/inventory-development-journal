# MODULE 1 — Employee accounts

## Chapter 10: Forgot and reset password (Steps 113–127)

### Before we begin

We already had a way to change a password while signed in. But someone who forgot their password could not use it.

### What we worked on

We built the full forgotten-password journey: request a link, display a confirmation, send a message, open the link, choose a new password and return to login.

### Picture it this way

There are several small pages because each has a different job. Django's built-in reset views handle token checks; our templates decide what the person sees.

### Find a step

- [Step 113: Forgot Password](#step-113)
- [Step 114: Password Reset URL](#step-114)
- [Step 115: Password Reset Confirmation](#step-115)
- [Step 116: Understand the password reset templates](#step-116)
- [Step 117: Confirmation Page URL](#step-117)
- [Step 118: Password Reset Email](#step-118)
- [Step 119: Connect Email Template](#step-119)
- [Step 120: Connect Email Subject](#step-120)

---

<a id="step-113"></a>

### Step 113: Forgot Password

#### Before this step

A signed-in person can change a known password, but someone who has forgotten it cannot use that screen. The preceding step, Step 112, covered email configuration, so this was the next part of the development sequence.

#### What we did and why

We started a separate “Forgot password?” page where a user can enter an email address. Django then handles the reset process without requiring the old password.

#### Code and commands

**Step 113, block 1.** This code sample or terminal command was used while working on forgot password. The content below remains in its original sequence for Step 113, block 1.

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

#### How the code works

**Block 1 (html, 23 lines).** In Step 113, forgot password involved `templates/accounts/password_reset.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Forgot Password</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Forgot Password</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Enter your registered email address to reset your password.</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `<button type="submit">Send Reset Link</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}">Back to Login</a>` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 22–23:** The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 113. For Step 113, the proposed way to check forgot password was to open the password-related page and try the expected form flow with a test account. Development continued in Step 114 before a complete result could be claimed.

#### Next

[Step 112: Email Configuration](../Module-1/09-email-validation-and-email-setup.md#step-112) · [Step 114: Password Reset URL](#step-114)


---

<a id="step-114"></a>

### Step 114: Password Reset URL

#### Before this step

The forgot-password form needed a web address and a Django view to handle its submission. Immediately before this, Step 113 dealt with forgot password; the work here continued the same part of the application.

#### What we did and why

Django provides a ready-made password-reset view. We connected it to our template, so the form can ask Django to prepare a reset email when submitted.

#### Code and commands

**Step 114, block 1.** This code sample or terminal command was used while working on password reset url. The content below remains in its original sequence for Step 114, block 1.

```python
from django.contrib.auth import views as auth_views
```

**Step 114, block 2.** This code sample or terminal command was used while working on password reset url. The content below remains in its original sequence for Step 114, block 2.

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html'
    ),
    name='password_reset'
),
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 114, password reset url involved `password_reset.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.contrib.auth import views as auth_views` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 7 lines).** In Step 114, password reset url involved `password_reset.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `path(` begins a URL rule connecting a browser address with the function that answers it. `template_name='accounts/password_reset.html'` sets the named value used by the next operation in this file. `name='password_reset'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 114. For Step 114, the proposed way to check password reset url was to open the related page in the browser and check that the route reached the intended view or template in `password_reset.html`. Development continued in Step 115 before a complete result could be claimed.

#### Next

[Step 113: Forgot Password](#step-113) · [Step 115: Password Reset Confirmation](#step-115)


---

<a id="step-115"></a>

### Step 115: Password Reset Confirmation

#### Before this step

After entering an email, a person should know the request has been accepted without being told whether the address belongs to an account. Immediately before this, Step 114 dealt with password reset url; the work here continued the same part of the application.

#### What we did and why

The confirmation page gives a neutral message. This helps people understand the next step while avoiding information that could help someone guess which employees are registered.

#### Code and commands

**Step 115, block 1.** This code sample or terminal command was used while working on password reset confirmation. The content below remains in its original sequence for Step 115, block 1.

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

#### How the code works

**Block 1 (html, 22 lines).** In Step 115, password reset confirmation involved `templates/accounts/password_reset_done.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Password Reset Requested</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Check Your Email</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `<p>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}">Back to Login</a>` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</body>` creates or closes part of the page structure seen by the browser.

**Lines 22–22:** The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 115. For Step 115, the proposed way to check password reset confirmation was to open the password-related page and try the expected form flow with a test account. Development continued in Step 116 before a complete result could be claimed.

#### Next

[Step 114: Password Reset URL](#step-114) · [Step 116: Understand the password reset templates](#step-116)


---

<a id="step-116"></a>

### Step 116: Understand the password reset templates

#### Before this step

The password-reset journey uses more than one screen, and their filenames were getting confusing. Immediately before this, Step 115 dealt with password reset confirmation; the work here continued the same part of the application.

#### What we did and why

We mapped the journey in order: enter email, see confirmation, open the link, set a new password, then see completion. Each template has one place in that journey.

#### Code and commands

**Step 116, block 1.** This code sample or terminal command was used while working on understand the password reset templates. The content below remains in its original sequence for Step 116, block 1.

```python
template_name='accounts/password_reset.html'
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 116, understand the password reset templates involved `login.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `template_name='accounts/password_reset.html'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 116. For Step 116, the proposed way to check understand the password reset templates was to open the related page in the browser and check that the route reached the intended view or template in `login.html`. Development continued in Step 117 before a complete result could be claimed.

#### Next

[Step 115: Password Reset Confirmation](#step-115) · [Step 117: Confirmation Page URL](#step-117)


---

<a id="step-117"></a>

### Step 117: Confirmation Page URL

#### Before this step

The password-reset view needed to know which page to show after the email request. Immediately before this, Step 116 dealt with understand the password reset templates; the work here continued the same part of the application.

#### What we did and why

We connected the confirmation URL as the next destination. Otherwise users could submit the form but end up on the wrong page or encounter a missing link.

#### Code and commands

**Step 117, block 1.** This code sample or terminal command was used while working on confirmation page url. The content below remains in its original sequence for Step 117, block 1.

```python
path(
    'password-reset/done/',
    auth_views.PasswordResetDoneView.as_view(
        template_name='accounts/password_reset_done.html'
    ),
    name='password_reset_done'
),
```

#### How the code works

**Block 1 (python, 7 lines).** In Step 117, confirmation page url involved `password_reset_done.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `path(` begins a URL rule connecting a browser address with the function that answers it. `template_name='accounts/password_reset_done.html'` sets the named value used by the next operation in this file. `name='password_reset_done'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 117. For Step 117, the proposed way to check confirmation page url was to open the related page in the browser and check that the route reached the intended view or template in `password_reset_done.html`. Development continued in Step 118 before a complete result could be claimed.

#### Next

[Step 116: Understand the password reset templates](#step-116) · [Step 118: Password Reset Email](#step-118)


---

<a id="step-118"></a>

### Step 118: Password Reset Email

#### Before this step

The email had to tell the user where to set a new password. Immediately before this, Step 117 dealt with confirmation page url; the work here continued the same part of the application.

#### What we did and why

The email template builds a link containing information Django can later check. It does not contain the person's password and it does not change the password just by being opened.

#### Code and commands

**Step 118, block 1.** This code sample or terminal command was used while working on password reset email. The content below remains in its original sequence for Step 118, block 1.

```django
Hello,

We received a request to reset your Inventory System password.

Click the link below to set a new password:

{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}

If you did not request this password reset, you can ignore this email.

Inventory System
```

**Step 118, block 2.** This code sample or terminal command was used while working on password reset email. The content below remains in its original sequence for Step 118, block 2.

```django
{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}
```

#### How the code works

**Block 1 (django, 11 lines).** In Step 118, password reset email involved `templates/accounts/password_reset_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 8–11:** The lines starting with `If you did not request this password reset, you can ignore this email.` supply the values or page content for this part of templates/accounts/password_reset_email.txt. These lines are kept together because their order and indentation matter.

**Block 2 (django, 1 lines).** In Step 118, password reset email involved `templates/accounts/password_reset_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}` builds the address from a Django route name, so the link follows the route if its address later changes.

#### How we checked it

Not tested yet at Step 118. For Step 118, the proposed way to check password reset email was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 119 before a complete result could be claimed.

#### Next

[Step 117: Confirmation Page URL](#step-117) · [Step 119: Connect Email Template](#step-119)


---

<a id="step-119"></a>

### Step 119: Connect Email Template

#### Before this step

We created the email text, but Django would not use it unless we explicitly pointed the reset view to it. Immediately before this, Step 118 dealt with password reset email; the work here continued the same part of the application.

#### What we did and why

The URL configuration names the email template. When Django prepares a reset message, it fills that template with the appropriate reset link.

#### Code and commands

**Step 119, block 1.** This code sample or terminal command was used while working on connect email template. The content below remains in its original sequence for Step 119, block 1.

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

**Step 119, block 2.** This code sample or terminal command was used while working on connect email template. The content below remains in its original sequence for Step 119, block 2.

```python
email_template_name='accounts/password_reset_email.txt'
```

#### How the code works

**Block 1 (python, 8 lines).** In Step 119, connect email template involved `password_reset_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `path(` begins a URL rule connecting a browser address with the function that answers it. `template_name='accounts/password_reset.html',` sets the named value used by the next operation in this file. `email_template_name='accounts/password_reset_email.txt'` sets the named value used by the next operation in this file. `name='password_reset'` sets the named value used by the next operation in this file.

**Lines 8–8:** The lines starting with `),` supply the values or page content for this part of password_reset_email.txt. These lines are kept together because their order and indentation matter.

**Block 2 (python, 1 lines).** In Step 119, connect email template involved `password_reset_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `email_template_name='accounts/password_reset_email.txt'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 119. For Step 119, the proposed way to check connect email template was to open the related page in the browser and check that the route reached the intended view or template in `password_reset_email.txt`. Development continued in Step 120 before a complete result could be claimed.

#### Next

[Step 118: Password Reset Email](#step-118) · [Step 120: Connect Email Subject](#step-120)


---

<a id="step-120"></a>

### Step 120: Connect Email Subject

#### Before this step

The reset message also needed a clear subject line so a recipient could recognise it. Immediately before this, Step 119 dealt with connect email template; the work here continued the same part of the application.

#### What we did and why

The subject is a small text template used as the email's title. This change helps people understand why they received the message; it does not change reset-link security.

#### Code and commands

**Step 120, block 1.** This code sample or terminal command was used while working on connect email subject. The content below remains in its original sequence for Step 120, block 1.

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

**Step 120, block 2.** This code sample or terminal command was used while working on connect email subject. The content below remains in its original sequence for Step 120, block 2.

```python
subject_template_name='accounts/password_reset_subject.txt'
```

#### How the code works

**Block 1 (python, 9 lines).** In Step 120, connect email subject involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `path(` begins a URL rule connecting a browser address with the function that answers it. `template_name='accounts/password_reset.html',` sets the named value used by the next operation in this file. `email_template_name='accounts/password_reset_email.txt',` sets the named value used by the next operation in this file. `subject_template_name='accounts/password_reset_subject.txt'` sets the named value used by the next operation in this file.

**Lines 8–9:** `name='password_reset'` sets the named value used by the next operation in this file.

**Block 2 (python, 1 lines).** In Step 120, connect email subject involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `subject_template_name='accounts/password_reset_subject.txt'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 120. For Step 120, the proposed way to check connect email subject was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 121 before a complete result could be claimed.

#### Next

[Step 119: Connect Email Template](#step-119) · [Step 121: Set New Password Page](#step-121)
