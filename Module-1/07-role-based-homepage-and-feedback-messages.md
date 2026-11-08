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

## Step 93: Admin Access

**No. Even before we added the `is_staff` condition to `home.html`, manager1 could not access Django's Admin panel.**

Here's why.

We created `manager1` with:

- `role = 'manager'`
- `is_staff = False`

Django already uses `is_staff` internally to decide who can access `/admin/`.

#### Before and after our change

| | Before | After |
|---|---|---|
| Manager sees "Open Django admin" link on homepage | Yes | No |
| Manager can access Django Admin panel | No | No |
| Admin superuser can access Django Admin panel | Yes | Yes |

#### So what did our code actually change?

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**It only hides the link from users who cannot access Django Admin anyway.**

We did not introduce a new security restriction. Django already had that restriction built in.

Also, when `manager1` visits `/admin/`, Django may show the Admin login screen, but **seeing that login screen does not mean they have permission to enter the Admin panel**.

*Previous: Step 92 (Admin Link Visibility)  |  Next: Step 94 (Admin Access Verified)*

---

## Step 94: Admin Access Verified

That is **the expected result**.

When `manager1` clicks **Open Django admin**:

1. The browser opens `/admin/`.
2. Django checks whether `manager1` has Admin access.
3. Because `is_staff=False`, Django displays the Admin login page instead of the dashboard.

So our existing access restriction is working as expected.

**The `is_staff` condition we added to `home.html` is only meant to hide the Admin link from manager1.** It does not create the access restriction.

One thing remains: your screenshot still shows that link for manager1. Make sure you have saved this change in `home.html`:

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

After refreshing the homepage, the link should disappear.

*Previous: Step 93 (Admin Access)  |  Next: Step 95 (Login Error Messages)*

---
