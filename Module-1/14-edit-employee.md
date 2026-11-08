# Module 1, Chapter 14: Edit employee

## Step 145: Edit Employee Form

#### Next: Create the Edit Employee form

Administrators should be able to update an employee's name, email and role without changing their password.

Open **`accounts/forms.py`**.

Add this new class below `EmployeeCreationForm`:

```python
class EmployeeEditForm(forms.ModelForm):

    class Meta:
        model = CustomUser
        fields = [
            'first_name',
            'last_name',
            'email',
            'role'
        ]

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]
```

#### Why a separate form?

| Creation form | Edit form |
|---|---|
| Creates a new account | Updates an existing account |
| Sets an unusable password | Preserves the existing password |
| Includes username | Keeps username unchanged |

  **NEW vs CLASS — Separate forms for different operations**

  Although both forms use `ModelForm`, they have different responsibilities.

  Reusing our creation form for editing would be dangerous because its `save()` method calls `set_unusable_password()`.

**Save `forms.py`.** No browser testing is needed yet.

Next, we'll create the `edit_employee` view.

*Previous: Step 144 (Change templates/accounts/employee_list.html)  |  Next: Step 146 (Edit Employee View)*

---

## Step 146: Edit Employee View

#### Next: Create the Edit Employee view

We've created `EmployeeEditForm`. Now we need a view that retrieves an existing employee, displays their details and saves changes.

Open **`accounts/views.py`**.

Add these imports at the top:

```python
from django.shortcuts import get_object_or_404
from .forms import EmployeeEditForm
```

Then add this function at the bottom:

```python
@role_required('admin')
def edit_employee(request, employee_id):

    employee = get_object_or_404(
        CustomUser,
        id=employee_id,
        role__in=['manager', 'sales']
    )

    if request.method == 'POST':
        form = EmployeeEditForm(
            request.POST,
            instance=employee
        )

        if form.is_valid():
            form.save()

            messages.success(
                request,
                'Employee updated successfully.'
            )

            return redirect('employee_list')

    else:
        form = EmployeeEditForm(instance=employee)

    return render(
        request,
        'accounts/edit_employee.html',
        {
            'form': form,
            'employee': employee
        }
    )
```

#### How does this work?

- `employee_id` identifies the employee being edited.
- `get_object_or_404()` retrieves that employee or returns HTTP 404 if no matching employee exists.
- `instance=employee` tells Django to **update the existing record**, rather than create a new one.
- Only Manager and Sales Staff accounts can be edited through this view.

  **NEW vs CLASS — `instance=employee`**

  Without `instance`, a `ModelForm` normally creates a new database record when saved.

  With `instance`, it updates the specified existing record.

**Save `views.py`.** We haven't connected the template or URL yet.

Next, we'll create `edit_employee.html`.

*Previous: Step 145 (Edit Employee Form)  |  Next: Step 147 (Edit Employee Template)*

---
