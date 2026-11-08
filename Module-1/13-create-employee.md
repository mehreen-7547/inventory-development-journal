# Module 1, Chapter 13: Create employee

## Step 139: Create Employee View

**Employee list tests passed.** Now we'll allow administrators to create employees directly from our inventory application.

#### Next: Create the employee registration view

Open **`accounts/views.py`**.

Add these imports at the top:

```python
from .forms import EmployeeCreationForm
from .utils import send_welcome_email
```

Then add this function at the bottom:

```python
@role_required('admin')
def create_employee(request):

    if request.method == 'POST':
        form = EmployeeCreationForm(request.POST)

        if form.is_valid():
            employee = form.save()

            send_welcome_email(request, employee)

            messages.success(
                request,
                'Employee created successfully.'
            )

            return redirect('employee_list')

    else:
        form = EmployeeCreationForm()

    return render(
        request,
        'accounts/create_employee.html',
        {'form': form}
    )
```

#### What happens here?

    **Admin-only access**

    **GET:** Display the employee registration form

    **POST:** Validate and save the employee

    **Send:** Generate the welcome email and setup link

    **Redirect:** Return to Manage Employees

We're reusing `EmployeeCreationForm` and `send_welcome_email()` from our earlier Django Admin implementation.

**For now:** Save `views.py`. Before connecting this page, we'll restrict the form's role choices to Manager and Sales Staff so this employee-registration workflow cannot create administrator accounts.

*Previous: Step 138 (Employee List URL)  |  Next: Step 140 (Restrict Employee Roles)*

---

## Step 140: Restrict Employee Roles

#### Next: Restrict roles in the employee creation form

Our `CustomUser` model supports three roles: Admin, Manager and Sales Staff.

However, the **Create Employee** page should only allow Manager and Sales Staff. Administrators shouldn't be able to create additional administrator accounts through this form.

Open **`accounts/forms.py`**.

Inside `EmployeeCreationForm`, add this method **between `Meta` and `save()`**:

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]
```

#### What does this do?

- `__init__()` — runs when Django creates an instance of our form.
- `super().__init__()` — performs the normal Django form initialization.
- `self.fields['role'].choices` — limits the available roles to Manager and Sales Staff.

This also enforces the restriction during form validation, not just in the dropdown.

  **NEW vs CLASS — `__init__()`**

  `__init__()` is Python's object initialization method.

  We're overriding it to customize the form after Django creates its fields, similar to how we previously overrode `save()`.

**Save the file.** The employee creation view can now use this restricted form.

Next, we'll create `create_employee.html`.

*Previous: Step 139 (Create Employee View)  |  Next: Step 141 (Create Employee Template)*

---
