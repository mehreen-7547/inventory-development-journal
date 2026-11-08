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
