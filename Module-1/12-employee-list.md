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
