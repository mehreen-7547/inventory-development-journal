# MODULE 1 — Employee accounts

## Chapter 13: Create employee (Steps 139–144)

### Before we begin

We could see employees in a list, but adding a new person still relied on Django's built-in administration page.

### What we worked on

We built our own Create Employee page with a form, a view that saves the account, role restrictions and a way to open the form from the list.

### Picture it this way

When an admin first opens the page, Django shows empty fields. After the admin submits them, Django validates the values, saves the employee and starts the welcome-email process. The view, template and URL work together to make the journey possible.

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

#### Before this step

We now had a page listing employees. An administrator needed a way to add another person from inside the application. The preceding step, Step 138, covered employee list url, so this was the next part of the development sequence.

#### What we did and why

The create view shows an empty form when opened and checks filled-in values when submitted. If they are valid, it saves the employee, tries to send the welcome message and returns to the list. The URL and HTML page are connected in following steps.

#### Code and commands

**Step 139, block 1.** This code sample or terminal command was used while working on create employee view. The content below remains in its original sequence for Step 139, block 1.

```python
from .forms import EmployeeCreationForm
from .utils import send_welcome_email
```

**Step 139, block 2.** This code sample or terminal command was used while working on create employee view. The content below remains in its original sequence for Step 139, block 2.

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

#### How the code works

**Block 1 (python, 2 lines).** In Step 139, create employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from .forms import EmployeeCreationForm` makes a Python function, class or module available to the code that follows. `from .utils import send_welcome_email` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 26 lines).** In Step 139, create employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `def create_employee(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied. `form = EmployeeCreationForm(request.POST)` sets the named value used by the next operation in this file.

**Lines 8–14:** `form.save()` writes the validated form values into the corresponding database record. `messages.success(` prepares feedback that can appear when the user reaches the next page.

**Lines 15–21:** `return redirect('employee_list')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. The `else` branch handles the alternative case when the preceding condition was not satisfied. `form = EmployeeCreationForm()` sets the named value used by the next operation in this file.

**Lines 22–26:** `return render(` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 139. For Step 139, the proposed way to check create employee view was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 140 before a complete result could be claimed.

#### Next

[Step 138: Employee List URL](../Module-1/12-employee-list.md#step-138) · [Step 140: Restrict Employee Roles](#step-140)


---

<a id="step-140"></a>

### Step 140: Restrict Employee Roles

#### Before this step

The creation form showed Admin as a possible role, but this staff form was only meant for Manager and Sales Staff. Immediately before this, Step 139 dealt with create employee view; the work here continued the same part of the application.

#### What we did and why

We changed the form so its role choices contain only those two jobs. Django checks the selected value when the form is submitted, so hiding Admin in the dropdown is not the only protection.

#### Code and commands

**Step 140, block 1.** This code sample or terminal command was used while working on restrict employee roles. The content below remains in its original sequence for Step 140, block 1.

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]
```

#### How the code works

**Block 1 (python, 7 lines).** In Step 140, restrict employee roles involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 140. For Step 140, the proposed way to check restrict employee roles was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 141 before a complete result could be claimed.

#### Next

[Step 139: Create Employee View](#step-139) · [Step 141: Create Employee Template](#step-141)


---

<a id="step-141"></a>

### Step 141: Create Employee Template

#### Before this step

The create view was ready, but administrators still needed the page where they could type an employee's information. Immediately before this, Step 140 dealt with restrict employee roles; the work here continued the same part of the application.

#### What we did and why

The template displays the creation form and a save button. If the input is missing or invalid, it can show the same form with errors so the administrator can correct it.

#### Code and commands

**Step 141, block 1.** This code sample or terminal command was used while working on create employee template. The content below remains in its original sequence for Step 141, block 1.

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

#### How the code works

**Block 1 (html, 30 lines).** In Step 141, create employee template involved `templates/accounts/create_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Create Employee</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Create Employee</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `<button type="submit">Create Employee</button>` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `<p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'employee_list' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 29–30:** The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 141. For Step 141, the proposed way to check create employee template was to open the related page in the browser and check that the route reached the intended view or template in `templates/accounts/create_employee.html`. Development continued in Step 142 before a complete result could be claimed.

#### Next

[Step 140: Restrict Employee Roles](#step-140) · [Step 142: Create Employee URL](#step-142)


---

<a id="step-142"></a>

### Step 142: Create Employee URL

#### Before this step

The creation page had a view and template but no address in the accounts URL file. Immediately before this, Step 141 dealt with create employee template; the work here continued the same part of the application.

#### What we did and why

The new route links the browser's address to the create view. Together, view, template and URL form a usable page.

#### Code and commands

**Step 142, block 1.** This code sample or terminal command was used while working on create employee url. The content below remains in its original sequence for Step 142, block 1.

```python
path(
    'employees/create/',
    views.create_employee,
    name='create_employee'
),
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 142, create employee url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `path(` begins a URL rule connecting a browser address with the function that answers it. `name='create_employee'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 142. For Step 142, the proposed way to check create employee url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Development continued in Step 143 before a complete result could be claimed.

#### Next

[Step 141: Create Employee Template](#step-141) · [Step 143: Add Employee Link](#step-143)


---

<a id="step-143"></a>

### Step 143: Add Employee Link

#### Before this step

The employee list was visible, but there was no obvious way to reach the new creation page. Immediately before this, Step 142 dealt with create employee url; the work here continued the same part of the application.

#### What we did and why

We added a “Create Employee” action to the list page. This is only navigation; the actual employee record is created by the protected view.

#### Code and commands

**Step 143, block 1.** This code sample or terminal command was used while working on add employee link. The content below remains in its original sequence for Step 143, block 1.

```html
<h2>Manage Employees</h2>
```

**Step 143, block 2.** This code sample or terminal command was used while working on add employee link. The content below remains in its original sequence for Step 143, block 2.

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

#### How the code works

**Block 1 (html, 1 lines).** In Step 143, add employee link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<h2>Manage Employees</h2>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 5 lines).** In Step 143, add employee link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** The HTML element `<p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'create_employee' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 143. For Step 143, the proposed way to check add employee link was to revisit `templates/accounts/employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 144 before a complete result could be claimed.

#### Next

[Step 142: Create Employee URL](#step-142) · [Step 144: Refine the Create Employee link](#step-144)


---

<a id="step-144"></a>

### Step 144: Refine the Create Employee link

#### Before this step

We adjusted where the create action appears so it would be easier to spot on the employee list. Immediately before this, Step 143 dealt with add employee link; the work here continued the same part of the application.

#### What we did and why

The link still leads to the same creation page. The change was about making the workflow clear, not adding a second way to save employees.

#### Code and commands

**Step 144, block 1.** This code sample or terminal command was used while working on refine the create employee link. The content below remains in its original sequence for Step 144, block 1.

```html
<h2>Manage Employees</h2>
```

**Step 144, block 2.** This code sample or terminal command was used while working on refine the create employee link. The content below remains in its original sequence for Step 144, block 2.

```html
<p>
    <a href="{% url 'create_employee' %}">
        Add New Employee
    </a>
</p>
```

#### How the code works

**Block 1 (html, 1 lines).** In Step 144, refine the create employee link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<h2>Manage Employees</h2>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 5 lines).** In Step 144, refine the create employee link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** The HTML element `<p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'create_employee' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 144. For Step 144, the proposed way to check refine the create employee link was to revisit `templates/accounts/employee_list.html` and run the corresponding command or feature to see its result. Step 144 did not establish a successful outcome for refine the create employee link.

#### Next

[Step 143: Add Employee Link](#step-143) · [Step 145: Edit Employee Form](../Module-1/14-edit-employee.md#step-145)


### Check your understanding

Without looking at the code, explain why **create employee** was needed at this point, and describe what the application did differently after the final step of this chapter.
