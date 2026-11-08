# Module 1, Chapter 10: Forgot and reset password

## Step 113: Forgot Password

#### Next: Create the Forgot Password page

We're beginning the **password recovery workflow**.

When an employee forgets their password, they should be able to enter their registered email address and request a reset link.

**Create a new file:**

`templates/accounts/password_reset.html`

Paste:

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

#### What is happening here?

- `method="POST"` — submits the entered email to Django.
- `{% csrf_token %}` — protects the form against CSRF attacks.
- `{{ form.as_p }}` — displays Django's built-in password-reset form, including its email field.
- **Send Reset Link** — submits the request.

We're reusing the same Django form-rendering approach as our Change Password page.

**For now:** Create and save the file. It won't be accessible yet because we haven't connected its URL and view.

*Previous: Step 112 (Email Configuration)  |  Next: Step 114 (Password Reset URL)*

---

## Step 114: Password Reset URL

#### Next: Connect the Forgot Password page to Django

We've created `password_reset.html`. Now we need a URL and a view to display it.

Open **`accounts/urls.py`**.

**1. Add this import** at the top:

```python
from django.contrib.auth import views as auth_views
```

**2. Add this entry** inside your existing `urlpatterns` list:

```python
path(
    'password-reset/',
    auth_views.PasswordResetView.as_view(
        template_name='accounts/password_reset.html'
    ),
    name='password_reset'
),
```

Keep your existing login, logout and change-password URLs unchanged.

#### What does this do?

- `password-reset/` — the address employees will visit.
- `PasswordResetView` — Django's built-in view for requesting a password-reset email.
- `as_view()` — makes this class-based view usable in our URL configuration.
- `template_name` — tells Django which HTML file to display.

  **NEW vs CLASS — Class-based views**

  So far, we've written views using `def`, such as `def loginpage(request)`.

  Django also provides ready-made views as classes. `PasswordResetView.as_view()` lets us reuse one without writing the entire password-reset process ourselves.

#### Test

Save the file and open:

`http://127.0.0.1:8000/password-reset/`

**Expected result:** Your Forgot Password page appears with an email field and a **Send Reset Link** button.

Don't submit the form yet. We still need to configure the email templates and remaining reset URLs.

*Previous: Step 113 (Forgot Password)  |  Next: Step 115 (Password Reset Confirmation)*

---

## Step 115: Password Reset Confirmation

#### Next: Create the password-reset confirmation page

After an employee submits their email address, Django should display a confirmation page.

**Create a new file:**

`templates/accounts/password_reset_done.html`

Paste:

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

#### Why this wording?

Notice we don't say *"An account with this email exists."*

That is intentional. **We shouldn't reveal whether a particular email address is registered in our system.**

The same confirmation message should appear whether the submitted email belongs to an active employee or not.

**For now:** Save the file. Next, we'll connect it to Django's password-reset workflow.

*Previous: Step 114 (Password Reset URL)  |  Next: Step 116 (Template Structure)*

---

## Step 116: Template Structure

Yes. **All three HTML files should be inside the same `templates/accounts/` folder.**

    **templates/**

      **accounts/**

      {#each ["login.html","change_password.html","password_reset.html","password_reset_done.html"] as file}

          {file}

      {/each}

The `accounts` folder groups templates related to authentication.

In `accounts/urls.py`, this path:

```python
template_name='accounts/password_reset.html'
```

tells Django to find `password_reset.html` inside the `accounts` template folder.

*Previous: Step 115 (Password Reset Confirmation)  |  Next: Step 117 (Confirmation Page URL)*

---

## Step 117: Confirmation Page URL

#### Next: Connect the confirmation page to Django

We've created `password_reset_done.html`. Now we need Django to display it after a password-reset request.

Open **`accounts/urls.py`**.

Add this inside your existing `urlpatterns` list:

```python
path(
    'password-reset/done/',
    auth_views.PasswordResetDoneView.as_view(
        template_name='accounts/password_reset_done.html'
    ),
    name='password_reset_done'
),
```

#### How does this work?

    **PasswordResetView**

    Processes the submitted email address

    **PasswordResetDoneView**

    Displays password_reset_done.html

Django's built-in `PasswordResetView` automatically looks for the URL named `password_reset_done` after processing a valid form submission.

#### Test

Save the file and visit:

`http://127.0.0.1:8000/password-reset/done/`

You should see the **Check Your Email** page.

**Do not submit the password-reset form yet.** We still need to create the email templates used to generate the secure reset link.

*Previous: Step 116 (Template Structure)  |  Next: Step 118 (Password Reset Email)*

---

## Step 118: Password Reset Email

#### Next: Create the password-reset email template

When an employee requests a password reset, Django needs to know **what message to send**.

Create a new file:

`templates/accounts/password_reset_email.txt`

Paste:

```django
Hello,

We received a request to reset your Inventory System password.

Click the link below to set a new password:

{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}

If you did not request this password reset, you can ignore this email.

Inventory System
```

#### Understanding the important line

```django
{{ protocol }}://{{ domain }}{% url 'password_reset_confirm' uidb64=uid token=token %}
```

Django will generate a link containing:

- `protocol` — HTTP or HTTPS.
- `domain` — the website's domain.
- `uid` — an encoded identifier for the employee's account.
- `token` — a security token used to validate the reset request.

  **NEW vs CLASS — Dynamic URL parameters**

  Earlier, we used `{% url 'login' %}` to generate a fixed URL.

  Here, `{% url 'password_reset_confirm' uidb64=uid token=token %}` generates a URL containing values specific to the password-reset request.

**For now:** Save the file. We haven't connected this email template or created the reset-link destination yet.

*Previous: Step 117 (Confirmation Page URL)  |  Next: Step 119 (Connect Email Template)*

---
