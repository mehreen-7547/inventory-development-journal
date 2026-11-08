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
