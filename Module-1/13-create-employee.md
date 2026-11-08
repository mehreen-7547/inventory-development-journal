# MODULE 1 — Employee accounts

## Chapter 13: Create employee (Steps 139–144)

### Before we begin

We could see employees in a list, but adding a new person still relied on Django's built-in administration page.

### What we worked on

We built our own Create Employee page with a form, a view that saves the account, role restrictions and a way to open the form from the list.

### Picture it this way

When an admin first opens the page, Django shows empty fields. After the admin submits them, Django validates the values, saves the employee and starts the welcome-email process. This is the same journey described in your example chapter.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 139: Create Employee View](#step-139)
- [Step 140: Restrict Employee Roles](#step-140)
- [Step 141: Create Employee Template](#step-141)
- [Step 142: Create Employee URL](#step-142)
- [Step 143: Add Employee Link](#step-143)
- [Step 144: Refine the Create Employee link](#step-144)

---

<a id="step-139"></a>

### Step 139: Create Employee View

**What was happening before this step?**

We now had a page listing employees. An administrator needed a way to add another person from inside the application.

**What did we do, and what does it mean?**

The create view shows an empty form when opened and checks filled-in values when submitted. If they are valid, it saves the employee, tries to send the welcome message and returns to the list. The URL and HTML page are connected in following steps.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add these imports at the top.*

```python
from .forms import EmployeeCreationForm
from .utils import send_welcome_email
```

*2. Then add this function at the bottom.*

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

**Understanding the code, one piece at a time**

There are two paths through the same function. On GET, the administrator has only opened the page, so Django creates an empty form. On POST, the administrator has clicked Save, so Django checks the filled form. A valid form saves the employee and attempts the welcome message. An invalid form shows the same fields with errors. The URL and HTML screen are connected in the following steps.

**How did we know what happened?**

The HTML page and named URL are added in Steps 141–142, so the complete feature is not yet testable here.

**Where to go next:** Previous: [Step 138 — Employee List URL](12-employee-list.md#step-138) · Next: [Step 140 — Restrict Employee Roles](13-create-employee.md#step-140)


---

<a id="step-140"></a>

### Step 140: Restrict Employee Roles

**What was happening before this step?**

The creation form showed Admin as a possible role, but this staff form was only meant for Manager and Sales Staff.

**What did we do, and what does it mean?**

We changed the form so its role choices contain only those two jobs. Django checks the selected value when the form is submitted, so hiding Admin in the dropdown is not the only protection.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Inside EmployeeCreationForm, add this method between Meta and save().*

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]
```

**Understanding the code, one piece at a time**

`__init__` runs whenever the form object is made. `super().__init__()` first gives us the normal Django fields. Then `self.fields['role'].choices` replaces the role options with Manager and Sales Staff. This is also checked when someone submits the form, not just when the dropdown is displayed.

**How did we know what happened?**

Once the page exists, confirm the dropdown excludes Admin and a forged Admin choice is rejected.

**Where to go next:** Previous: [Step 139 — Create Employee View](13-create-employee.md#step-139) · Next: [Step 141 — Create Employee Template](13-create-employee.md#step-141)


---

<a id="step-141"></a>

### Step 141: Create Employee Template

**What was happening before this step?**

The create view was ready, but administrators still needed the page where they could type an employee's information.

**What did we do, and what does it mean?**

The template displays the creation form and a save button. If the input is missing or invalid, it can show the same form with errors so the administrator can correct it.

**File or place:** `templates/accounts/create_employee.html`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

The route is added in Step 142 before the page can be opened normally.

**Where to go next:** Previous: [Step 140 — Restrict Employee Roles](13-create-employee.md#step-140) · Next: [Step 142 — Create Employee URL](13-create-employee.md#step-142)


---

<a id="step-142"></a>

### Step 142: Create Employee URL

**What was happening before this step?**

The creation page had a view and template but no address in the accounts URL file.

**What did we do, and what does it mean?**

The new route links the browser's address to the create view. Together, view, template and URL form a usable page.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside the existing urlpatterns list.*

```python
path(
    'employees/create/',
    views.create_employee,
    name='create_employee'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The next step makes the entry point visible from the employee list.

**Where to go next:** Previous: [Step 141 — Create Employee Template](13-create-employee.md#step-141) · Next: [Step 143 — Add Employee Link](13-create-employee.md#step-143)


---

<a id="step-143"></a>

### Step 143: Add Employee Link

**What was happening before this step?**

The employee list was visible, but there was no obvious way to reach the new creation page.

**What did we do, and what does it mean?**

We added a “Create Employee” action to the list page. This is only navigation; the actual employee record is created by the protected view.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Code or command used at this point.*

```html
<h2>Manage Employees</h2>
```

*2. Add.*

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

**How did we know what happened?**

From the list, select Add Employee and confirm that the create form opens.

**Where to go next:** Previous: [Step 142 — Create Employee URL](13-create-employee.md#step-142) · Next: [Step 144 — Refine the Create Employee link](13-create-employee.md#step-144)


---

<a id="step-144"></a>

### Step 144: Refine the Create Employee link

**What was happening before this step?**

We adjusted where the create action appears so it would be easier to spot on the employee list.

**What did we do, and what does it mean?**

The link still leads to the same creation page. The change was about making the workflow clear, not adding a second way to save employees.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Find.*

```html
<h2>Manage Employees</h2>
```

*2. Immediately below it, add.*

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

**How did we know what happened?**

Check that the link is visible only where intended and opens the existing create route.

**Where to go next:** Previous: [Step 143 — Add Employee Link](13-create-employee.md#step-143) · Next: [Step 145 — Edit Employee Form](14-edit-employee.md#step-145)

---

### The point of this chapter

When an admin first opens the page, Django shows empty fields. After the admin submits them, Django validates the values, saves the employee and starts the welcome-email process. This is the same journey described in your example chapter.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

