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
