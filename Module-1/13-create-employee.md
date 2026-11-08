# MODULE 1 — Accounts

## Chapter 13: Create employee (Steps 139–144)

**Where we are.** An employee list is useful only if an administrator can add someone through the application.

**What this chapter gives us.** Connect a creation form, GET/POST view, limited role choices, template, route, and Add Employee link.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain what the create view does on GET, valid POST, and invalid POST.

### Find a step

- [Step 139: Create Employee View](#step-139)
- [Step 140: Restrict Employee Roles](#step-140)
- [Step 141: Create Employee Template](#step-141)
- [Step 142: Create Employee URL](#step-142)
- [Step 143: Add Employee Link](#step-143)
- [Step 144: Refine the Create Employee link](#step-144)

---

<a id="step-139"></a>

### Step 139: Create Employee View

**Why we are doing this.** From the employee list, an admin should be able to open a custom page and save a new employee.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these imports at the top.*

```python
from .forms import EmployeeCreationForm
from .utils import send_welcome_email
```

*2. Recorded snippet; Then add this function at the bottom.*

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

**How it works.** The create view branches on GET versus POST: GET shows an empty form; POST validates, saves, sends a welcome email, displays a message, and redirects. A failed form is shown again with errors.

**Follow the flow.** First, `@role_required('admin')` prevents a non-admin from entering this page. A browser GET request creates an empty `EmployeeCreationForm` and displays it. Submitting the form sends POST data to the same view. `is_valid()` checks the values, and `save()` writes the employee. The welcome-email helper prepares onboarding, then `messages.success` stores feedback and `redirect('employee_list')` returns to the list. Invalid form data skips saving and returns the form with errors. The HTML template and URL are connected in the next steps.

**How we checked it.** The HTML page and named URL are added in Steps 141–142, so the complete feature is not yet testable here.

**Connects to.** Previous: [Step 138 — Employee List URL](12-employee-list.md#step-138) · Next: [Step 140 — Restrict Employee Roles](13-create-employee.md#step-140)


---

<a id="step-140"></a>

### Step 140: Restrict Employee Roles

**Why we are doing this.** The initial creation form offered all user roles, including Admin, but administrators should not create additional administrators through this staff page.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Inside EmployeeCreationForm, add this method between Meta and save().*

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]
```

**How it works.** Overriding form initialization narrows the role choices to Manager and Sales. Django forms validate submitted choices on the server as well as displaying a dropdown.

**Follow the flow.** `__init__` runs every time Django constructs this form. `super().__init__` creates its normal fields first; we then replace only the `role` field's available choices. The result is a dropdown with Manager and Sales Staff, excluding Admin. This is not just a browser display restriction: Django validates the allowed choices again when a POST is submitted.

**How we checked it.** Once the page exists, confirm the dropdown excludes Admin and a forged Admin choice is rejected.

**Connects to.** Previous: [Step 139 — Create Employee View](13-create-employee.md#step-139) · Next: [Step 141 — Create Employee Template](13-create-employee.md#step-141)


---

<a id="step-141"></a>

### Step 141: Create Employee Template

**Why we are doing this.** The create view needs a real browser form so the administrator can enter employee details and see errors.

**File / location.** `templates/accounts/create_employee.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** The template displays form fields, uses CSRF protection for POST, and provides a navigation path back to the list.

The token helps Django reject forged state-changing form submissions.

**How we checked it.** The route is added in Step 142 before the page can be opened normally.

**Connects to.** Previous: [Step 140 — Restrict Employee Roles](13-create-employee.md#step-140) · Next: [Step 142 — Create Employee URL](13-create-employee.md#step-142)


---

<a id="step-142"></a>

### Step 142: Create Employee URL

**Why we are doing this.** The create-employee view and template must be attached to a URL to become accessible.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside the existing urlpatterns list.*

```python
path(
    'employees/create/',
    views.create_employee,
    name='create_employee'
),
```

**How it works.** The named `create_employee` path points to the function created in Step 139, completing the form submission route.

**How we checked it.** The next step makes the entry point visible from the employee list.

**Connects to.** Previous: [Step 141 — Create Employee Template](13-create-employee.md#step-141) · Next: [Step 143 — Add Employee Link](13-create-employee.md#step-143)


---

<a id="step-143"></a>

### Step 143: Add Employee Link

**Why we are doing this.** The employee list was functional, but it lacked an obvious action for adding a new employee.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```html
<h2>Manage Employees</h2>
```

*2. Recorded snippet; Add.*

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

**How it works.** The template link uses the named create route, avoiding duplicated hardcoded paths.

**How we checked it.** From the list, select Add Employee and confirm that the create form opens.

**Connects to.** Previous: [Step 142 — Create Employee URL](13-create-employee.md#step-142) · Next: [Step 144 — Refine the Create Employee link](13-create-employee.md#step-144)


---

<a id="step-144"></a>

### Step 144: Refine the Create Employee link

**Why we are doing this.** After adding a create link, we refined where that action appears within the employee list page.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find.*

```html
<h2>Manage Employees</h2>
```

*2. Recorded snippet; Immediately below it, add.*

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

**How it works.** This template edit adjusts the existing navigation rather than creating another view. Later steps will add editing and selection behavior.

**How we checked it.** Check that the link is visible only where intended and opens the existing create route.

**Connects to.** Previous: [Step 143 — Add Employee Link](13-create-employee.md#step-143) · Next: [Step 145 — Edit Employee Form](14-edit-employee.md#step-145)

---

**Chapter recap.** Connect a creation form, GET/POST view, limited role choices, template, route, and Add Employee link.

**Practice explaining:** Explain what the create view does on GET, valid POST, and invalid POST.

