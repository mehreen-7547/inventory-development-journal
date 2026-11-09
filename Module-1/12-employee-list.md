# MODULE 1 — Employee accounts

## Chapter 12: Employee list (Steps 136–138)

### Before we begin

Employee accounts existed, but an administrator needed one place inside the website to see them together.

### What we worked on

We created a view that reads employee records, an HTML table that displays them and a URL that opens the list.

### Picture it this way

Think of the employee list as the starting screen for account management. At this stage it mostly shows information; the next chapters add ways to create and edit accounts.

### Find a step

- [Step 136: Employee Management](#step-136)
- [Step 137: Employee List Template](#step-137)
- [Step 138: Employee List URL](#step-138)

---

<a id="step-136"></a>

### Step 136: Employee Management

#### Before this step

We could create employee accounts, but administrators still needed a screen where they could see them together. The preceding step, Step 135, covered resend setup link, so this was the next part of the development sequence.

#### What we did and why

The employee-list view asks the database for staff accounts and passes the results to an HTML template. A list is the starting point for later create, edit and status actions.

#### Code and commands

**Step 136, block 1.** This code sample or terminal command was used while working on employee management. The content below remains in its original sequence for Step 136, block 1.

```python
from .models import CustomUser
from .decorators import role_required
```

**Step 136, block 2.** This code sample or terminal command was used while working on employee management. The content below remains in its original sequence for Step 136, block 2.

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

#### How the code works

**Block 1 (python, 2 lines).** In Step 136, employee management involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `from .decorators import role_required` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 11 lines).** In Step 136, employee management involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `def employee_list(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `employees = CustomUser.objects.filter(` sets the named value used by the next operation in this file. `role__in=['manager', 'sales']` sets the named value used by the next operation in this file.

**Lines 8–11:** The lines starting with `request,` supply the values or page content for this part of accounts/views.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 136. For Step 136, the proposed way to check employee management was to revisit `accounts/views.py` and run the corresponding command or feature to see its result. Development continued in Step 137 before a complete result could be claimed.

#### Next

[Step 135: Resend Setup Link](../Module-1/11-employee-welcome-email-and-password-setup-link.md#step-135) · [Step 137: Employee List Template](#step-137)


---

<a id="step-137"></a>

### Step 137: Employee List Template

#### Before this step

The view had employee records, but a browser needs visible rows and columns to show them. Immediately before this, Step 136 dealt with employee management; the work here continued the same part of the application.

#### What we did and why

The HTML template goes through the employees one at a time and prints their names, roles or account state. It displays data that the view provided; it does not create or update records.

#### Code and commands

**Step 137, block 1.** This code sample or terminal command was used while working on employee list template. The content below remains in its original sequence for Step 137, block 1.

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

#### How the code works

**Block 1 (html, 44 lines).** In Step 137, employee list template involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Manage Employees</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Manage Employees</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<table border="1" cellpadding="10">` creates or closes part of the page structure seen by the browser. The HTML element `<tr>` creates or closes part of the page structure seen by the browser. The HTML element `<th>Username</th>` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `<th>Role</th>` creates or closes part of the page structure seen by the browser. The HTML element `<th>Status</th>` creates or closes part of the page structure seen by the browser. The HTML element `</tr>` creates or closes part of the page structure seen by the browser. `{% for employee in employees %}` repeats the following operation once for each matching item.

**Lines 22–28:** The HTML element `<td>{{ employee.first_name }} {{ employee.last_name }}</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>{{ employee.email }}</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>{{ employee.get_role_display }}</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** The HTML element `</td>` creates or closes part of the page structure seen by the browser. The HTML element `</tr>` creates or closes part of the page structure seen by the browser. The HTML element `<tr>` creates or closes part of the page structure seen by the browser. The HTML element `<td colspan="5">No employees found.</td>` creates or closes part of the page structure seen by the browser.

**Lines 36–42:** The HTML element `</tr>` creates or closes part of the page structure seen by the browser. The HTML element `</table>` creates or closes part of the page structure seen by the browser. `<p><a href="{% url 'home' %}">Back to Home</a></p>` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 43–44:** The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 137. For Step 137, the proposed way to check employee list template was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 138 before a complete result could be claimed.

#### Next

[Step 136: Employee Management](#step-136) · [Step 138: Employee List URL](#step-138)


---

<a id="step-138"></a>

### Step 138: Employee List URL

#### Before this step

The list view and page existed, but an administrator needed a URL to open them. Immediately before this, Step 137 dealt with employee list template; the work here continued the same part of the application.

#### What we did and why

Adding the named employee-list route makes the page available and gives other pages a stable link back to it.

#### Code and commands

**Step 138, block 1.** This code sample or terminal command was used while working on employee list url. The content below remains in its original sequence for Step 138, block 1.

```python
path(
    'employees/',
    views.employee_list,
    name='employee_list'
),
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 138, employee list url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `path(` begins a URL rule connecting a browser address with the function that answers it. `name='employee_list'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 138. For Step 138, the proposed way to check employee list url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Step 138 did not establish a successful outcome for employee list url.

#### Next

[Step 137: Employee List Template](#step-137) · [Step 139: Create Employee View](../Module-1/13-create-employee.md#step-139)


### Check your understanding

Without looking at the code, explain why **employee list** was needed at this point, and describe what the application did differently after the final step of this chapter.
