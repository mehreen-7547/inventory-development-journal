# Module 1, Chapter 18: Shared base template and page styling

## Step 179: Dashboard Template Inheritance

Your existing `home.html` is correct functionally. We only need to connect it to `base.html` and remove the duplicated navigation and messages.

#### Step 1 — Replace `home.html`

**File:** `core/templates/core/home.html`

Replace the entire contents with:

```html
{% extends 'base.html' %}

{% block title %}Dashboard - Inventory System{% endblock %}

{% block content %}

<h1>Inventory &amp; Demand Intelligence</h1>

<!-- WELCOME -->
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>

<!-- DASHBOARD -->
{% if request.user.role == 'admin' %}

    <h3>Administrator Dashboard</h3>

    {% if request.user.is_staff %}
        <p>
            <a href="/admin/">Open Django admin</a>
        </p>
    {% endif %}

{% elif request.user.role == 'manager' %}

    <h3>Inventory Manager Dashboard</h3>

{% elif request.user.role == 'sales' %}

    <h3>Sales Staff Dashboard</h3>

{% endif %}

{% endblock %}
```

#### Step 2 — Understand the changes

| Before | Now |
|---|---|
| Repeated HTML structure | Inherited from `base.html` |
| Separate logout form | Shared navbar handles logout |
| Separate Change Password link | Shared navbar handles it |
| Separate messages section | Shared layout displays messages |
| Role-specific dashboard | Preserved |
| Django Admin link | Preserved for staff users |

#### Step 3 — Test

Run the Django server if it isn't already running:

```powershell
python manage.py runserver
```

Open `http://127.0.0.1:8000/`.

Verify that the dashboard displays the Bootstrap navbar, welcome message, and correct role-specific heading. Check that **Manage Employees**, **Change Password**, and **Logout** appear only once.

*Previous: Step 178 (Change base.html)  |  Next: Step 180 (Update Login Page)*

---

## Step 180: Update Login Page

Your existing login form is correct. We'll connect it to `base.html`, apply basic Bootstrap styling, and add a link to the password-reset feature we've already implemented.

#### Step 1 — Replace `login.html`

**File:** `templates/accounts/login.html`

Replace the entire contents with:

```html
{% extends 'base.html' %}

{% block title %}Login - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-4">

        <h2 class="mb-4">Login</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                <label for="username" class="form-label">
                    Username
                </label>
                <input type="text"
                       id="username"
                       name="username"
                       class="form-control"
                       placeholder="Enter username"
                       autocomplete="username"
                       required>
            </div>

            <div class="mb-3">
                <label for="password" class="form-label">
                    Password
                </label>
                <input type="password"
                       id="password"
                       name="password"
                       class="form-control"
                       placeholder="Enter password"
                       autocomplete="current-password"
                       required>
            </div>

            <button type="submit" class="btn btn-primary w-100">
                Login
            </button>
        </form>

        <p class="mt-3 text-center">
            <a href="{% url 'password_reset' %}">
                Forgot Password?
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

#### Step 2 — What changed?

- The page now inherits the shared Bootstrap layout and navbar.
- The login form is centred with basic Bootstrap styling.
- Messages are displayed by `base.html`, so we don't repeat them here.
- The **Forgot Password?** link opens our existing password-reset page.
- Username, password, and CSRF protection remain unchanged.

#### Step 3 — Test

Open `http://127.0.0.1:8000/login/` after logging out.

Verify that the form is centred, the Login button works, the Forgot Password link opens correctly, and an incorrect password displays an error message without duplicating it.

*Previous: Step 179 (Dashboard Template Inheritance)  |  Next: Step 181 (Update Change Password Page)*

---

## Step 181: Update Change Password Page

#### Step 1 — Replace `change_password.html`

**File:** `templates/accounts/change_password.html`

Replace the entire contents with:

```html
{% extends 'base.html' %}

{% block title %}Change Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-4">Change Password</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Change Password
            </button>

            <a href="{% url 'home' %}"
               class="btn btn-outline-secondary">
                Cancel
            </a>
        </form>

    </div>
</div>

{% endblock %}
```

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and buttons.
- Added a **Cancel** link to the dashboard.
- Preserved `{{ form.as_p }}`, including Django's password validation and field errors.
- Removed the duplicated HTML structure.

#### Step 3 — Test

1. Log in and select **Change Password**.
2. Enter an incorrect current password.
3. Submit the form.
4. Verify that Django displays a validation error and does not change the password.
5. Click **Cancel** and verify that you return to the dashboard.

This is still the functional Bootstrap interface, not the final visual design.

*Previous: Step 180 (Update Login Page)  |  Next: Step 182 (Update Forgot Password Page)*

---

## Step 182: Update Forgot Password Page

#### Step 1 — Replace the existing HTML

**File:** The password-reset form template you just opened.

Replace its entire contents with:

```html
{% extends 'base.html' %}

{% block title %}Forgot Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-3">Forgot Password</h2>

        <p>
            Enter your registered email address
            to reset your password.
        </p>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Send Reset Link
            </button>
        </form>

        <p class="mt-3">
            <a href="{% url 'login' %}">
                Back to Login
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

#### Step 2 — What changed?

- Uses the shared `base.html` layout.
- Centres the form using Bootstrap.
- Styles the submit button.
- Preserves Django's email field, validation, and CSRF protection.
- Keeps the **Back to Login** link.

#### Step 3 — Test

1. Open the login page.
2. Click **Forgot Password?**
3. Confirm the updated page displays correctly.
4. Click **Back to Login** and verify that it works.
5. Return and submit a registered test email address. Verify that the password-reset confirmation page appears.

*Previous: Step 181 (Update Change Password Page)  |  Next: Step 183 (Update Reset Confirmation)*

---

## Step 183: Update Reset Confirmation

#### Step 1 — Replace `password_reset_done.html`

**File:** `templates/accounts/password_reset_done.html`

Replace the entire contents with:

```html
{% extends 'base.html' %}

{% block title %}Password Reset Requested - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-6">

        <h2 class="mb-4">Check Your Email</h2>

        <div class="alert alert-info">
            If an active account exists with that email address,
            password reset instructions will be sent.
        </div>

        <p>
            Please check your email for the reset link.
        </p>

        <a href="{% url 'login' %}"
           class="btn btn-outline-primary mt-2">
            Back to Login
        </a>

    </div>
</div>

{% endblock %}
```

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and an information alert.
- Styled the **Back to Login** link.
- Preserved the existing confirmation message.

**Important:** The message intentionally does not reveal whether an email address belongs to an account. This helps prevent account enumeration.

#### Step 3 — Test

1. Open **Forgot Password**.
2. Submit a test email address.
3. Verify that the **Check Your Email** page appears.
4. Click **Back to Login** and confirm that it works.

*Previous: Step 182 (Update Forgot Password Page)  |  Next: Step 184 (Update Set New Password Page)*

---
