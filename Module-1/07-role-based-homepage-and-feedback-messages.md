# Module 1, Chapter 7: Role-based homepage and feedback messages

## Step 91: Role-Based Homepage

#### Next: Display different content based on user role

Our system has three roles: Admin, Inventory Manager, and Sales Staff.

Open **`core/templates/core/home.html`**.

Below the welcome message, add:

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

#### How it works

Django checks the logged-in user's `role` field and displays the matching message.

For example:

  Example: Logged in as manager1
  Welcome, manager1
  Role: Inventory Manager

  **Inventory Manager Dashboard**

**Important:** This only changes what users *see*. It does not yet prevent them from accessing restricted functionality. We'll implement actual role permissions separately.

**Test:** Log in as `manager1` and `sales1` separately. Each should see their corresponding dashboard heading.

*Previous: Step 90 (Change Password Link)  |  Next: Step 92 (Admin Link Visibility)*

---

## Step 92: Admin Link Visibility

#### Next: Show the Django Admin link only to authorized staff

Currently, everyone sees the **Open Django admin** link, even though they cannot necessarily access it.

Open **`core/home.html`**.

Replace:

```html
<p><a href="/admin/">Open Django admin</a></p>
```

With:

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

#### How it works

`request.user.is_staff` checks whether the logged-in user has Django staff access.

- `admin` (superuser) → sees the link.
- `manager1` → doesn't see the link.
- `sales1` → doesn't see the link.

**Important:** `is_staff` is a Django permission field. It is different from our custom `role` field.

**Expected result:** The admin link appears only for staff users.

This only controls link visibility. Django itself enforces access restrictions on `/admin/`.

*Previous: Step 91 (Role-Based Homepage)  |  Next: Step 93 (Admin Access)*

---
