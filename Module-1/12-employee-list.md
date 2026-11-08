# Module 1, Chapter 12: Employee list

## Step 136: Employee Management

We'll build a **Manage Employees** section inside the inventory application, accessible only to administrators.

It will support creating employees, changing roles, deactivating/reactivating accounts, and resending password-setup links.

#### Next: Create the Employee List view

Open `accounts/views.py`.

Add these imports:

```python
from .models import CustomUser
from .decorators import role_required
```

Then add this function at the bottom:

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

#### What does this do?

- `@role_required('admin')` — prevents non-admin users from accessing this page.
- `filter(role__in=...)` — retrieves managers and sales employees from the database.
- `order_by('username')` — sorts them alphabetically.
- `render()` — passes the employees to an HTML template.

  **NEW vs CLASS — 1 concept**

  `role__in` is a Django ORM lookup that checks whether a field matches any value in a supplied list.

  Here, it means: retrieve users whose role is either `manager` or `sales`.

**Save `views.py`.** Next, we'll create the HTML page that displays these employees.

*Previous: Step 135 (Resend Setup Link)  |  Next: Step 137 (Employee List Template)*

---

## Step 137: Employee List Template

#### Next: Create the Manage Employees page

We've created `employee_list()` in `accounts/views.py`. Now we'll create the HTML page that displays employees.

Create:

**`templates/accounts/employee_list.html`**

Paste:

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

#### What does this display?

Each employee will appear as a row containing their username, name, email, role and account status.

  **NEW vs CLASS — `{% empty %}`**

  We already know `{% for %}` loops through records.

  `{% empty %}` is Django's built-in way to display a message when there are no records to loop through.

**Save the file.** No URL is connected yet, so we won't test it in the browser now.

Next, we'll add the URL for this page.

*Previous: Step 136 (Employee Management)  |  Next: Step 138 (Employee List URL)*

---
