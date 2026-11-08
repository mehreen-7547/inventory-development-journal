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
