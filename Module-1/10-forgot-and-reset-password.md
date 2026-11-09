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
