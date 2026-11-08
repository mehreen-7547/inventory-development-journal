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

## Step 147: Edit Employee Template

#### Next: Create the Edit Employee page

We've created the `edit_employee()` view. Now we'll create the HTML form that administrators will use to update employee details.

Create:

**`templates/accounts/edit_employee.html`**

Paste:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Edit Employee</title>
</head>
<body>

    <h2>Edit Employee</h2>

    <p>Username: {{ employee.username }}</p>

    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit">
            Save Changes
        </button>
    </form>

    <p>
        <a href="{% url 'employee_list' %}">
            Back to Manage Employees
        </a>
    </p>

</body>
</html>
```

#### What will this page do?

- Display the employee's username (read-only).
- Show their existing name, email and role.
- Allow the administrator to modify those details.
- Save changes without affecting the employee's password.

**Save the file.** The page isn't accessible yet because we haven't added its URL.

Next, we'll connect the Edit Employee URL.

*Previous: Step 146 (Edit Employee View)  |  Next: Step 148 (Edit Employee URL)*

---

## Step 148: Edit Employee URL

#### Next: Connect the Edit Employee page

Open **`accounts/urls.py`**.

Add this inside your existing `urlpatterns` list:

```python
path(
    'employees/edit/<int:employee_id>/',
    views.edit_employee,
    name='edit_employee'
),
```

#### What does this URL mean?

For example:

`http://127.0.0.1:8000/employees/edit/3/`

Django extracts `3` from the URL and passes it to our view as `employee_id`.

  **NEW vs CLASS — `<int:employee_id>`**

  This is a Django URL path converter.

  - `int` means the value must be an integer.
  - `employee_id` is the variable name passed to the view.

  This allows one view to edit different employees.

#### Test

1. Log in as Admin.
2. Open `/employees/edit/3/`, replacing `3` with an existing employee's ID.
3. Change the employee's first name or role.
4. Click **Save Changes**.
5. Confirm the changes appear in `/employees/`.

Also verify that Sales Staff cannot access the edit page (HTTP 403).

**Note:** The employee list currently displays usernames, not database IDs. You can find an employee's ID in Django Admin. We'll add an **Edit** link beside each employee next.

*Previous: Step 147 (Edit Employee Template)  |  Next: Step 149 (Employee Edit Link)*

---

## Step 149: Employee Edit Link

#### Next: Add an Edit button to each employee

The custom Edit Employee page is working. Now we'll make it accessible directly from the employee list, without needing to find employee IDs manually.

Open **`templates/accounts/employee_list.html`**.

**1.** Find the table headings and add:

```html
<th>Actions</th>
```

Place it immediately after:

```html
<th>Status</th>
```

**2.** Inside the `{% for employee in employees %}` loop, add this cell immediately after the employee's status `</td>`:

```html
<td>
    <a href="{% url 'edit_employee' employee.id %}">
        Edit
    </a>
</td>
```

**3.** Find the empty-table message:

```html
<td colspan="5">No employees found.</td>
```

Change `colspan="5"` to `colspan="6"` because the table now has six columns.

#### Expected result

| Username | Role | Status | Actions |
|---|---|---|---|
| manager1 | Inventory Manager | Active | Edit |
| sales1 | Sales Staff | Active | Edit |

Illustrative table — your page will still show Name and Email.

#### Test

1. Open `/employees/` as Admin.
2. Click **Edit** beside any employee.
3. Confirm that the correct employee's details appear.
4. Click **Back to Manage Employees**.

**Save the file and test.** Next, we'll implement employee account deactivation.

*Previous: Step 148 (Edit Employee URL)  |  Next: Step 150 (Employee Edit Mode)*

---

## Step 150: Employee Edit Mode

#### Step 1: Add an Edit Mode

We want this workflow:

      Click Edit Employee

      Select employee

      Edit details

First, Django needs to know whether the employee list is in normal mode or edit mode.

Open **`accounts/views.py`**.

Find the `employee_list()` function and replace only its `return render(...)` statement with:

```python
    return render(
        request,
        'accounts/employee_list.html',
        {
            'employees': employees,
            'edit_mode': request.GET.get('mode') == 'edit'
        }
    )
```

#### How it works

| URL | Edit mode |
|---|---|
| `/employees/` | False |
| `/employees/?mode=edit` | True |

  **NEW vs CLASS — `request.GET.get()`**

  Reads a value from the URL's query parameters.

  Here, Django checks whether `mode` equals `edit` and sends the resulting `True` or `False` to our HTML template.

**Save `views.py`.** The page won't look different yet; we'll use `edit_mode` in the next step.

*Previous: Step 149 (Employee Edit Link)  |  Next: Step 151 (Edit Mode Button)*

---
