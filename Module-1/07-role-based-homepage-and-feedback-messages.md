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
