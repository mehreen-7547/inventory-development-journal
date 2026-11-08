# MODULE 1 — Accounts

## Chapter 14: Edit employee (Steps 145–156)

**Where we are.** Employee information changes over time. We added an edit form and then improved how administrators select the right employee.

**What this chapter gives us.** Use ModelForm instances and ID-based URLs; follow the evolution of edit mode and row-selection fixes.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why editing needs an existing model instance instead of creating a new user.

### Find a step

- [Step 145: Edit Employee Form](#step-145)
- [Step 146: Edit Employee View](#step-146)
- [Step 147: Edit Employee Template](#step-147)
- [Step 148: Edit Employee URL](#step-148)
- [Step 149: Employee Edit Link](#step-149)
- [Step 150: Employee Edit Mode](#step-150)
- [Step 151: Edit Mode Button](#step-151)
- [Step 152: Select Employee](#step-152)
- [Step 153: Combine the list and selection controls](#step-153)
- [Step 154: External CSS](#step-154)
- [Step 155: Fix Row Selection](#step-155)
- [Step 156: Correct employee row selection](#step-156)

---

<a id="step-145"></a>

### Step 145: Edit Employee Form

**Why we are doing this.** Administrators also need to change an existing employee's details without creating a new account.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this new class below EmployeeCreationForm.*

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

**How it works.** `EmployeeEditForm` is bound to an existing `CustomUser` instance and controls which fields can be edited. Password changes are kept separate from ordinary employee details.

**Follow the flow.** The edit form is based on the same model as the creation form but exposes only the fields administrators are allowed to change. In particular, editing ordinary profile details should not silently create a new employee or replace the employee's password. The view will bind the form to an existing record in the next step.

**How we checked it.** The edit view that loads and saves the form is added in Step 146.

**Connects to.** Previous: [Step 144 — Refine the Create Employee link](13-create-employee.md#step-144) · Next: [Step 146 — Edit Employee View](14-edit-employee.md#step-146)


---

<a id="step-146"></a>

### Step 146: Edit Employee View

**Why we are doing this.** The edit form needs a view that finds the correct employee and handles both initial display and submitted changes.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these imports at the top.*

```python
from django.shortcuts import get_object_or_404
from .forms import EmployeeEditForm
```

*2. Recorded snippet; Then add this function at the bottom.*

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

**How it works.** The view uses a user identifier to fetch the employee, passes it as `instance=...`, validates POST data, saves updates, and redirects with a notice.

**Follow the flow.** The view gets an `employee_id` from the URL and looks up the corresponding Manager or Sales user. If no match exists, `get_object_or_404` returns a 404. On GET, `EmployeeEditForm(instance=employee)` pre-fills current values. On POST, `EmployeeEditForm(request.POST, instance=employee)` edits *that same row*. Successful validation saves changes and redirects to the list; errors are shown on the form. The admin decorator keeps editing restricted.

**How we checked it.** The corresponding template and URL are implemented in Steps 147–148.

**Connects to.** Previous: [Step 145 — Edit Employee Form](14-edit-employee.md#step-145) · Next: [Step 147 — Edit Employee Template](14-edit-employee.md#step-147)


---

<a id="step-147"></a>

### Step 147: Edit Employee Template

**Why we are doing this.** An edit view cannot be used comfortably without a page showing the current values and save controls.

**File / location.** `templates/accounts/edit_employee.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** The edit template renders the bound form and includes a CSRF token. Field validation errors remain visible on the same page.

The token helps Django reject forged state-changing form submissions.

**How we checked it.** The URL and navigation are connected in the next steps.

**Connects to.** Previous: [Step 146 — Edit Employee View](14-edit-employee.md#step-146) · Next: [Step 148 — Edit Employee URL](14-edit-employee.md#step-148)


---

<a id="step-148"></a>

### Step 148: Edit Employee URL

**Why we are doing this.** The edit-employee view needs a route that tells it which employee is being edited.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside your existing urlpatterns list.*

```python
path(
    'employees/edit/<int:employee_id>/',
    views.edit_employee,
    name='edit_employee'
),
```

**How it works.** A URL parameter such as the employee ID is passed into the view. Django resolves that path to a record or returns not found when no matching record exists.

**How we checked it.** A link or selection mechanism must still provide the correct ID from the employee list.

**Connects to.** Previous: [Step 147 — Edit Employee Template](14-edit-employee.md#step-147) · Next: [Step 149 — Employee Edit Link](14-edit-employee.md#step-149)


---

<a id="step-149"></a>

### Step 149: Employee Edit Link

**Why we are doing this.** Administrators need a way to reach an individual edit page from the employee list.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find the table headings and add.*

```html
<th>Actions</th>
```

*2. Recorded snippet; Place it immediately after.*

```html
<th>Status</th>
```

*3. Recorded snippet; Inside the {% for employee in employees %} loop, add this cell immediately after the employee's status </td>.*

```html
<td>
    <a href="{% url 'edit_employee' employee.id %}">
        Edit
    </a>
</td>
```

*4. Recorded snippet; Find the empty-table message.*

```html
<td colspan="5">No employees found.</td>
```

**How it works.** A template link builds the named edit URL with that employee's ID. This connects the displayed row to the edit view.

**How we checked it.** The next steps explore a selection-based editing interface.

**Connects to.** Previous: [Step 148 — Edit Employee URL](14-edit-employee.md#step-148) · Next: [Step 150 — Employee Edit Mode](14-edit-employee.md#step-150)


---

<a id="step-150"></a>

### Step 150: Employee Edit Mode

**Why we are doing this.** The first edit link worked, but the page design was changed to make editing an explicit action instead of accidental navigation.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find the employeelist() function and replace only its return render(...) statement with.*

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

**How it works.** An edit-mode state controls when employee rows can be selected for modification. This is a user-interface workflow on top of the same edit view.

**How we checked it.** The selection behavior is completed and corrected over Steps 151–156.

**Connects to.** Previous: [Step 149 — Employee Edit Link](14-edit-employee.md#step-149) · Next: [Step 151 — Edit Mode Button](14-edit-employee.md#step-151)


---

<a id="step-151"></a>

### Step 151: Edit Mode Button

**Why we are doing this.** We introduced an Edit control so a user deliberately enters employee-selection mode.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Immediately above the <table> tag, add.*

```html
{% if edit_mode %}

    <p>Select an employee to edit.</p>

    <a href="{% url 'employee_list' %}">
        Cancel Edit
    </a>

{% else %}

    <p>
        <a href="{% url 'employee_list' %}?mode=edit">
            Edit Employee
        </a>
    </p>

{% endif %}
```

**How it works.** The button changes the page's interaction state; JavaScript or template logic then decides which row should open editing.

**How we checked it.** The next step implements selecting a specific employee.

**Connects to.** Previous: [Step 150 — Employee Edit Mode](14-edit-employee.md#step-150) · Next: [Step 152 — Select Employee](14-edit-employee.md#step-152)


---

<a id="step-152"></a>

### Step 152: Select Employee

**Why we are doing this.** Once edit mode was available, the administrator needed to choose one of the listed employees.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Inside <head>, add.*

```html
<style>
    .selectable {
        position: relative;
        cursor: pointer;
    }

    .selectable:hover {
        background-color: #eeeeee;
    }

    .row-link::after {
        content: "";
        position: absolute;
        inset: 0;
    }
</style>
```

*2. Recorded snippet; Find the <tr> immediately after.*

```html
{% for employee in employees %}
```

*3. Recorded snippet; Replace that <tr> with.*

```html
<tr {% if edit_mode %}class="selectable"{% endif %}>
```

*4. Recorded snippet; Find the username cell.*

```html
<td>{{ employee.username }}</td>
```

*5. Recorded snippet; Replace it with.*

```html
<td>
    {% if edit_mode %}
        <a class="row-link"
           href="{% url 'edit_employee' employee.id %}">
            {{ employee.username }}
        </a>
    {% else %}
        {{ employee.username }}
    {% endif %}
</td>
```

**How it works.** Row selection stores or follows the chosen employee ID, then uses the existing edit route. The distinction between selection and submission avoids editing the wrong user.

**How we checked it.** The fuller template version and a selection fix follow in Steps 153–156.

**Connects to.** Previous: [Step 151 — Edit Mode Button](14-edit-employee.md#step-151) · Next: [Step 153 — Combine the list and selection controls](14-edit-employee.md#step-153)


---

<a id="step-153"></a>

### Step 153: Combine the list and selection controls

**Why we are doing this.** The earlier list template fragments needed to be combined into one functioning employee table with edit-mode controls.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents of templates/accounts/employeelist.html with this.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Manage Employees</title>

    <style>
        .selectable {
            position: relative;
            cursor: pointer;
        }

        .selectable:hover {
            background-color: #eeeeee;
        }

        .row-link::after {
            content: "";
            position: absolute;
            inset: 0;
        }
    </style>
</head>

<body>

    <h2>Manage Employees</h2>

    <p>
        <a href="{% url 'create_employee' %}">
            Add New Employee
        </a>
    </p>

    {% if edit_mode %}

        <p>Select an employee to edit.</p>

        <a href="{% url 'employee_list' %}">
            Cancel Edit
        </a>

    {% else %}

        <p>
            <a href="{% url 'employee_list' %}?mode=edit">
                Edit Employee
            </a>
        </p>

    {% endif %}

    <br>

    <table border="1" cellpadding="10">

        <tr>
            <th>Username</th>
            <th>Name</th>
            <th>Email</th>
            <th>Role</th>
            <th>Status</th>
        </tr>

        {% for employee in employees %}

        <tr {% if edit_mode %}class="selectable"{% endif %}>

            <td>
                {% if edit_mode %}
                    <a class="row-link"
                       href="{% url 'edit_employee' employee.id %}">
                        {{ employee.username }}
                    </a>
                {% else %}
                    {{ employee.username }}
                {% endif %}
            </td>

            <td>
                {{ employee.first_name }}
                {{ employee.last_name }}
            </td>

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

    <p>
        <a href="{% url 'home' %}">
            Back to Home
        </a>
    </p>

</body>
</html>
```

**How it works.** This replacement updates the HTML and associated JavaScript together. Read it as a later version of the same page, not an additional employee-list feature.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** The selection behavior still needed the corrections noted in subsequent steps.

**Connects to.** Previous: [Step 152 — Select Employee](14-edit-employee.md#step-152) · Next: [Step 154 — External CSS](14-edit-employee.md#step-154)


---

<a id="step-154"></a>

### Step 154: External CSS

**Why we are doing this.** The growing amount of list styling made the template harder to read and maintain.

**File / location.** `accounts/static/accounts/style.css`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this to accounts/static/accounts/style.css.*

```css
.selectable {
    position: relative;
    cursor: pointer;
}

.selectable:hover {
    background-color: #eeeeee;
}

.row-link::after {
    content: "";
    position: absolute;
    inset: 0;
}
```

*2. Recorded snippet; Add this at the very beginning.*

```django
{% load static %}
```

*3. Recorded snippet; Replace the entire existing <head>...</head> section with.*

```html
<head>
    <title>Manage Employees</title>
    <link rel="stylesheet"
          href="{% static 'accounts/style.css' %}">
</head>
```

**How it works.** Moving CSS into an external static stylesheet separates layout from HTML behavior. `{% load static %}` and a stylesheet link let Django serve that file.

**How we checked it.** Confirm the static file is loaded and the table retains its appearance.

**Connects to.** Previous: [Step 153 — Combine the list and selection controls](14-edit-employee.md#step-153) · Next: [Step 155 — Fix Row Selection](14-edit-employee.md#step-155)


---

<a id="step-155"></a>

### Step 155: Fix Row Selection

**Why we are doing this.** The row-selection interaction did not behave as intended in its first implementation.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find.*

```html
{% for employee in employees %}
```

*2. Recorded snippet.*

```html
<tr {% if edit_mode %}class="selectable"{% endif %}>
```

*3. Recorded snippet; Then replace the existing username <td>...</td> with.*

```html
<td>
    {% if edit_mode %}
        <a class="row-link"
           href="{% url 'edit_employee' employee.id %}">
            {{ employee.username }}
        </a>
    {% else %}
        {{ employee.username }}
    {% endif %}
</td>
```

**How it works.** The JavaScript/markup update changes how an employee row becomes selected and when an Edit action is enabled. The row's ID must remain tied to the correct employee.

**How we checked it.** This is a bug-fix step, not a new permission rule.

**Connects to.** Previous: [Step 154 — External CSS](14-edit-employee.md#step-154) · Next: [Step 156 — Correct employee row selection](14-edit-employee.md#step-156)


---

<a id="step-156"></a>

### Step 156: Correct employee row selection

**Why we are doing this.** After identifying the selection bug, we replaced the affected list template with a corrected version.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire file with.*

```html
{% load static %}

<!DOCTYPE html>
<html>
<head>
    <title>Manage Employees</title>
    <link rel="stylesheet"
          href="{% static 'accounts/style.css' %}">
</head>

<body>

    <h2>Manage Employees</h2>

    <p>
        <a href="{% url 'create_employee' %}">
            Add New Employee
        </a>
    </p>

    {% if edit_mode %}

        <p>Select an employee to edit.</p>

        <a href="{% url 'employee_list' %}">
            Cancel Edit
        </a>

    {% else %}

        <p>
            <a href="{% url 'employee_list' %}?mode=edit">
                Edit Employee
            </a>
        </p>

    {% endif %}

    <br>

    <table border="1" cellpadding="10">

        <tr>
            <th>Username</th>
            <th>Name</th>
            <th>Email</th>
            <th>Role</th>
            <th>Status</th>
        </tr>

        {% for employee in employees %}

        <tr {% if edit_mode %}class="selectable"{% endif %}>

            <td>
                {% if edit_mode %}
                    <a class="row-link"
                       href="{% url 'edit_employee' employee.id %}">
                        {{ employee.username }}
                    </a>
                {% else %}
                    {{ employee.username }}
                {% endif %}
            </td>

            <td>
                {{ employee.first_name }}
                {{ employee.last_name }}
            </td>

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

    <p>
        <a href="{% url 'home' %}">
            Back to Home
        </a>
    </p>

</body>
</html>
```

**How it works.** The updated HTML integrates selection classes, buttons, and event handling consistently. It supersedes earlier list-template fragments.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** Test selecting different rows and confirm the correct employee opens in edit mode.

**Connects to.** Previous: [Step 155 — Fix Row Selection](14-edit-employee.md#step-155) · Next: [Step 157 — Employee Deactivation](15-activate-deactivate-and-resend-setup-email.md#step-157)

---

**Chapter recap.** Use ModelForm instances and ID-based URLs; follow the evolution of edit mode and row-selection fixes.

**Practice explaining:** Explain why editing needs an existing model instance instead of creating a new user.

