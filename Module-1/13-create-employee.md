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

## Step 141: Create Employee Template

#### Next: Create the employee registration page

We've created the view and restricted the available roles. Now we'll build the HTML form.

Create:

**`templates/accounts/create_employee.html`**

Paste:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Create Employee</title>
</head>
<body>

    <h2>Create Employee</h2>

    <p>
        Enter the employee details below.
        A password setup link will be sent to their email.
    </p>

    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit">Create Employee</button>
    </form>

    <p>
        <a href="{% url 'employee_list' %}">
            Back to Manage Employees
        </a>
    </p>

</body>
</html>
```

#### What will the page contain?

- Username
- First name and last name
- Email address
- Role: Inventory Manager or Sales Staff
- Create Employee button

**No password field is required.** Employees will set their own passwords using the welcome email.

We're reusing `{{ form.as_p }}` and `{% csrf_token %}` from our earlier forms.

**For now:** Save the file. Next, we'll connect its URL and test employee creation through our application's interface.

*Previous: Step 140 (Restrict Employee Roles)  |  Next: Step 142 (Create Employee URL)*

---

## Step 142: Create Employee URL

#### Next: Connect the Create Employee page

Open **`accounts/urls.py`**.

Add this inside the existing `urlpatterns` list:

```python
path(
    'employees/create/',
    views.create_employee,
    name='create_employee'
),
```

#### Test

1. Run your Django server.
2. Log in as Admin.
3. Open `http://127.0.0.1:8000/employees/create/`
4. Create a test employee using a new username and unique email address. Select **Sales Staff**.
5. Confirm that you're redirected to the employee list and the new employee appears there.

Also check your VS Code terminal. Since we're currently using Django's console email backend, the **welcome email and password setup link** should appear there rather than in an actual inbox.

**Security check:** Log in as Sales Staff and try opening `/employees/create/`. You should receive HTTP 403.

  **Expected result**

  The Admin can create an employee without setting their password. The employee receives a setup link (currently displayed in the terminal), while Sales Staff cannot access employee creation.

*Previous: Step 141 (Create Employee Template)  |  Next: Step 143 (Add Employee Link)*

---

## Step 143: Add Employee Link

#### Next: Connect employee creation to the employee list

Both employee creation and access tests passed.

Currently, administrators must manually enter `/employees/create/` to create an employee. Let's add a link to the **Manage Employees** page.

Open **`templates/accounts/employee_list.html`**.

Immediately below:

```html
<h2>Manage Employees</h2>
```

Add:

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

#### Test

1. Log in as Admin.
2. Open `http://127.0.0.1:8000/employees/`.
3. Click **Add New Employee**.
4. Verify that the employee registration form opens.

  **Our employee management interface so far**

      Employee List
      /employees/

      Create Employee
      /employees/create/

Next, we'll implement **Edit Employee**, allowing administrators to update employee details and roles.

*Previous: Step 142 (Create Employee URL)  |  Next: Step 144 (Change templates/accounts/employee_list.html)*

---

## Step 144: Change templates/accounts/employee_list.html

Your screenshot shows the **existing Employee List page**, but the **Add New Employee** link hasn't been added yet.

Open `templates/accounts/employee_list.html`.

Find:

```html
<h2>Manage Employees</h2>
```

Immediately below it, add:

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

Save the file (`Ctrl + S`) and refresh your browser (`Ctrl + R`).

You should now see **Add New Employee** above the table. Clicking it should open the employee registration form.

*Previous: Step 143 (Add Employee Link)  |  Next: Step 145 (Edit Employee Form)*

---
