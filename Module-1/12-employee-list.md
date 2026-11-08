# MODULE 1 — Employee accounts

## Chapter 12: Employee list (Steps 136–138)

### Before we begin

Employee accounts existed, but an administrator needed one place inside the website to see them together.

### What we worked on

We created a view that reads employee records, an HTML table that displays them and a URL that opens the list.

### Picture it this way

Think of the employee list as the starting screen for account management. At this stage it mostly shows information; the next chapters add ways to create and edit accounts.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 136: Employee Management](#step-136)
- [Step 137: Employee List Template](#step-137)
- [Step 138: Employee List URL](#step-138)

---

<a id="step-136"></a>

### Step 136: Employee Management

**What was happening before this step?**

We could create employee accounts, but administrators still needed a screen where they could see them together.

**What did we do, and what does it mean?**

The employee-list view asks the database for staff accounts and passes the results to an HTML template. A list is the starting point for later create, edit and status actions.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add these imports.*

```python
from .models import CustomUser
from .decorators import role_required
```

*2. Then add this function at the bottom.*

```python
@role_required('admin')
def employee_list(request):
    employees = CustomUser.objects.filter(
        role__in=['manager', 'sales']
    ).order_by('username')

    return render(
        request,
        'accounts/employee_list.html',
        {'employees': employees}
    )
```

**Understanding the code, one piece at a time**

The view uses Django's model system to fetch employee records. It places them into the information passed to the HTML page. The page can show this list but cannot edit records just by displaying them.

**How did we know what happened?**

The HTML table and URL are added in Steps 137–138.

**Where to go next:** Previous: [Step 135 — Resend Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-135) · Next: [Step 137 — Employee List Template](12-employee-list.md#step-137)


---

<a id="step-137"></a>

### Step 137: Employee List Template

**What was happening before this step?**

The view had employee records, but a browser needs visible rows and columns to show them.

**What did we do, and what does it mean?**

The HTML template goes through the employees one at a time and prints their names, roles or account state. It displays data that the view provided; it does not create or update records.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Paste.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Manage Employees</title>
</head>
<body>

    <h2>Manage Employees</h2>

    <table border="1" cellpadding="10">
        <tr>
            <th>Username</th>
            <th>Name</th>
            <th>Email</th>
            <th>Role</th>
            <th>Status</th>
        </tr>

        {% for employee in employees %}
        <tr>
            <td>{{ employee.username }}</td>
            <td>{{ employee.first_name }} {{ employee.last_name }}</td>
            <td>{{ employee.email }}</td>
            <td>{{ employee.get_role_display }}</td>
            <td>
                {% if employee.is_active %}
                    Active
                {% else %}
                    Inactive
                {% endif %}
            </td>
        </tr>
        {% empty %}
        <tr>
            <td colspan="5">No employees found.</td>
        </tr>
        {% endfor %}

    </table>

    <p><a href="{% url 'home' %}">Back to Home</a></p>

</body>
</html>
```

**Understanding the code, one piece at a time**

A template loop means “repeat this small piece of HTML once for each employee.” Inside the loop, the employee's name, role and status are printed into the table.

**How did we know what happened?**

The page becomes reachable when the list URL is configured.

**Where to go next:** Previous: [Step 136 — Employee Management](12-employee-list.md#step-136) · Next: [Step 138 — Employee List URL](12-employee-list.md#step-138)


---

<a id="step-138"></a>

### Step 138: Employee List URL

**What was happening before this step?**

The list view and page existed, but an administrator needed a URL to open them.

**What did we do, and what does it mean?**

Adding the named employee-list route makes the page available and gives other pages a stable link back to it.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside your existing urlpatterns list.*

```python
path(
    'employees/',
    views.employee_list,
    name='employee_list'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The create-employee workflow begins next.

**Where to go next:** Previous: [Step 137 — Employee List Template](12-employee-list.md#step-137) · Next: [Step 139 — Create Employee View](13-create-employee.md#step-139)

---

### The point of this chapter

Think of the employee list as the starting screen for account management. At this stage it mostly shows information; the next chapters add ways to create and edit accounts.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

