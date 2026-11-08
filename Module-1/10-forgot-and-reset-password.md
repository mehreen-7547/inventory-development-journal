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

## Step 119: Connect Email Template

#### Next: Tell Django which email template to use

We've created `password_reset_email.txt`, but Django doesn't know to use it yet.

Open **`accounts/urls.py`**.

Find your existing `PasswordResetView` and update it to:

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

#### What changed?

We added one line:

```python
email_template_name='accounts/password_reset_email.txt'
```

Django now knows which template contains the password-reset email message.

The two templates serve different purposes:

| Setting | Purpose |
|---|---|
| `template_name` | HTML page where the employee enters their email |
| `email_template_name` | Text message Django sends with the reset link |

**Save the file.** Don't submit the form yet. We still need to configure the email subject and the page where employees set their new password.

*Previous: Step 118 (Password Reset Email)  |  Next: Step 120 (Connect Email Subject)*

---

## Step 120: Connect Email Subject

#### Next: Connect the email subject template

Open **`accounts/urls.py`**.

Find your existing `PasswordResetView` and update it to:

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

#### What changed?

We added:

```python
subject_template_name='accounts/password_reset_subject.txt'
```

`subject_template_name` is another **built-in Django attribute**, just like `email_template_name`.

Django now knows where to find all three templates:

| Attribute | File |
|---|---|
| `template_name` | Password-reset request page |
| `email_template_name` | Email body |
| `subject_template_name` | Email subject |

**Save the file.** Next, we'll create the page employees reach after clicking their password-reset link.

*Previous: Step 119 (Connect Email Template)  |  Next: Step 121 (Set New Password Page)*

---

## Step 121: Set New Password Page

#### Next: Create the Set New Password page

When an employee clicks the link in their password-reset email, they should reach a page where they can enter a new password.

Create:

`templates/accounts/password_reset_confirm.html`

Paste:

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

#### How does this work?

Django's built-in `PasswordResetConfirmView` will provide two important values:

- **`validlink`** — `True` if the reset link is valid; otherwise `False`.
- **`form`** — Django's form for entering and confirming a new password.

  **NEW vs CLASS — `validlink`**

  `validlink` is a built-in template context variable supplied by `PasswordResetConfirmView`.

  We use `{% if validlink %}` to show the password form only when the reset link is valid.

**For now:** Save the file. Next, we'll connect it to the URL contained in the reset email.

*Previous: Step 120 (Connect Email Subject)  |  Next: Step 122 (Reset Link URL)*

---

## Step 122: Reset Link URL

#### Next: Connect the Set New Password page

We've created `password_reset_confirm.html`. Now Django needs a URL to open it when an employee clicks the email link.

Open **`accounts/urls.py`**.

Add this inside your existing `urlpatterns`:

```python
path(
    'password-reset/confirm/<uidb64>/<token>/',
    auth_views.PasswordResetConfirmView.as_view(
        template_name='accounts/password_reset_confirm.html'
    ),
    name='password_reset_confirm'
),
```

#### Understanding the code

- `PasswordResetConfirmView` — Django's built-in view that validates the reset link and handles setting a new password.
- `template_name` — specifies the HTML page we created.
- `uidb64` — identifies the account whose password is being reset.
- `token` — allows Django to verify that the reset link is valid.

  **NEW vs CLASS — Dynamic URL segments**

  Earlier, our URLs were fixed, such as `login/`.

  Here, `<uidb64>` and `<token>` are **dynamic URL segments**. Django extracts their values from the incoming URL and passes them to the view.

  This is the same concept as a product detail URL containing a product ID.

#### How the pieces connect

    **Password-reset email**

    Contains the generated reset link

    **PasswordResetConfirmView**

    Checks the account and security token

    **password_reset_confirm.html**

    Displays the new-password form if the link is valid

**Save the file.** Next, we'll create the success page displayed after the password has been reset.

*Previous: Step 121 (Set New Password Page)  |  Next: Step 123 (Password Reset Success Page)*

---

## Step 123: Password Reset Success Page

#### Next: Create the Password Reset Success page

After an employee successfully sets a new password, Django should display a confirmation message.

Create a new file:

`templates/accounts/password_reset_complete.html`

Paste:

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

#### Where does this page fit?

    **Password Reset Email**

    **Set New Password**

      **Password Reset Complete**

    The page we're creating now

    **Login**

**Save the file.** Next, we'll connect it using Django's built-in `PasswordResetCompleteView`.

*Previous: Step 122 (Reset Link URL)  |  Next: Step 124 (Password Reset Complete URL)*

---

## Step 124: Password Reset Complete URL

#### Next: Connect the Password Reset Success page

We've created `password_reset_complete.html`. Now we need Django to display it after an employee successfully resets their password.

Open **`accounts/urls.py`**.

Add this inside your existing `urlpatterns`:

```python
path(
    'password-reset/complete/',
    auth_views.PasswordResetCompleteView.as_view(
        template_name='accounts/password_reset_complete.html'
    ),
    name='password_reset_complete'
),
```

#### How does this work?

- **`PasswordResetCompleteView`** — Django's built-in view for displaying the successful password-reset message.
- **`template_name`** — specifies the HTML file to display.
- **`name='password_reset_complete'`** — the URL name Django's `PasswordResetConfirmView` uses after saving the new password.

We don't need to write our own redirect logic.

#### Test

Save the file and visit:

`http://127.0.0.1:8000/password-reset/complete/`

You should see **Password Reset Successful** with a link to the login page.

This verifies that the page works, but **not yet that a password has actually been reset**.

Next, we'll test the complete password-reset workflow.

*Previous: Step 123 (Password Reset Success Page)  |  Next: Step 125 (Fix Django Error)*

---

## Step 125: Fix Django Error

#### What went wrong?

Your terminal shows:

```text
TypeError: PasswordResetDoneView() received an invalid
keyword 'email_template_name'.
```

You appear to have added `email_template_name` to **`PasswordResetDoneView` instead of `PasswordResetView`**.

Only `PasswordResetView` sends the email. `PasswordResetDoneView` simply displays the confirmation page.

#### Fix

Open `accounts/urls.py` and make sure these two URL entries look exactly like this:

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

**Important:** Both `email_template_name` and `subject_template_name` belong to `PasswordResetView`, not `PasswordResetDoneView`.

#### Verify

Save the file and run:

```powershell
python manage.py runserver
```

Expected:

```text
System check identified no issues (0 silenced).
Starting development server at http://127.0.0.1:8000/
```

Once the server starts successfully, we can resume testing the password-reset email.

*Previous: Step 124 (Password Reset Complete URL)  |  Next: Step 126 (Change accounts/urls.py)*

---

## Step 126: Change accounts/urls.py

Replace the entire contents of **`accounts/urls.py`** with this corrected version:

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

**Correction made:** Moved `email_template_name` and `subject_template_name` from `PasswordResetDoneView` to `PasswordResetView`.

After saving, restart the Django server if necessary and visit:

`http://127.0.0.1:8000/password-reset/`

Enter an active test account's email address and submit. The reset email should appear in your server terminal.

*Previous: Step 125 (Fix Django Error)  |  Next: Step 127 (Email Testing)*

---

## Step 127: Email Testing

#### 1. Why didn't you receive the email?

Because we configured Django to use:

```python
EMAIL_BACKEND = 'django.core.mail.backends.console.EmailBackend'
```

This is Django's **console email backend**.

- Django generates the email.
- The email is printed in your VS Code terminal.
- **Nothing is actually sent to your inbox**, even when you use your real email address.

This is expected behaviour, not an error.

For now, we'll continue using console emails to test password-reset links. Actual email delivery requires configuring an SMTP server or email service later.

#### 2. VS Code terminal shortcuts

| Action | Shortcut |
|---|---|
| Show/hide terminal panel | **Ctrl + `** |
| Create a new terminal | Ctrl + Shift + ` |
| Close the active terminal | Type `exit` (when no process is running) |

The backtick (`) key is usually located below **Esc**.

**Important:** Hiding the terminal doesn't stop your Django server. It continues running in the background.

*Previous: Step 126 (Change accounts/urls.py)  |  Next: Step 128 (Employee Creation Form)*

---
