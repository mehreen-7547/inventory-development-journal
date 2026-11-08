# Module 1, Chapter 15: Activate, deactivate and resend setup email

## Step 157: Employee Deactivation

#### Next: Create the Deactivate/Reactivate Employee view

Employee selection and editing are working.

Now we'll allow administrators to **deactivate employees who leave the company**, without deleting their accounts or historical records.

We'll eventually add a Deactivate/Reactivate button to the existing Edit Employee page.

#### Step 1: Add the view

Open **`accounts/views.py`**.

Add this import:

```python
from django.views.decorators.http import require_POST
```

Then add this function at the bottom:

```python
@role_required('admin')
@require_POST
def toggle_employee_status(request, employee_id):

    employee = get_object_or_404(
        CustomUser,
        id=employee_id,
        role__in=['manager', 'sales']
    )

    employee.is_active = not employee.is_active
    employee.save(update_fields=['is_active'])

    if employee.is_active:
        messages.success(
            request,
            'Employee activated successfully.'
        )
    else:
        messages.success(
            request,
            'Employee deactivated successfully.'
        )

    return redirect('employee_list')
```

#### What happens?

| Action | Result |
|---|---|
| Deactivate | Employee cannot log in |
| Reactivate | Employee can log in again using their existing password |
| Either action | Employee record remains in the database |

  **NEW vs CLASS — `@require_POST`**

  This decorator ensures the view accepts only POST requests.

  Changing an employee's account status is a database operation, so it should not happen simply by opening a URL.

**Save `views.py`.** No browser testing yet.

Next, we'll add the URL for this view.

*Previous: Step 156 (Change employee_list.html)  |  Next: Step 158 (Employee Status URL)*

---

## Step 158: Employee Status URL

#### Next: Connect the Deactivate/Reactivate view

We've created `toggle_employee_status()` in `accounts/views.py`.

Now we'll add a URL that connects to it.

Open **`accounts/urls.py`**.

Add this inside the existing `urlpatterns` list:

```python
path(
    'employees/status/<int:employee_id>/',
    views.toggle_employee_status,
    name='toggle_employee_status'
),
```

#### How does this work?

For example:

`/employees/status/3/`

Django passes employee ID `3` to `toggle_employee_status()`.

The view then changes the employee's status:

      **Active**
      Can log in

      **Inactive**
      Cannot log in

**Important:** This URL accepts only POST requests because we used `@require_POST`. Opening it directly in the browser will return HTTP 405 (Method Not Allowed). That is expected.

Save `accounts/urls.py`.

Next, we'll add the **Deactivate/Reactivate** button to `edit_employee.html`.

*Previous: Step 157 (Employee Deactivation)  |  Next: Step 159 (Deactivate/Reactivate Button)*

---
