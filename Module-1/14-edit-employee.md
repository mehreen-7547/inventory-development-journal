# MODULE 1 — Employee accounts

## Chapter 14: Edit employee (Steps 145–156)

### Before we begin

Administrators could add people, but correcting one employee's details was still awkward.

### What we worked on

We created an edit form and view, linked each employee to their edit page and improved how people select a row on the employee list.

### Picture it this way

Editing is not creating a second employee. The view finds the existing account by its ID and saves new values to that same record. Later steps in this chapter fix the page's selection controls and move styling into CSS.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

An administrator sometimes needs to correct an employee's details without creating a new account.

**What did we do, and what does it mean?**

We defined a separate edit form with the fields that may be changed. It works with an existing user record and does not ask for a new login password.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Add this new class below EmployeeCreationForm.*

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

**Understanding the code, one piece at a time**

The edit form is linked to the same user model as the create form, but it works with an existing account. It should not silently create a second account or change a password when an administrator only wanted to update an employee's name.

**How did we know what happened?**

The edit view that loads and saves the form is added in Step 146.

**Where to go next:** Previous: [Step 144 — Refine the Create Employee link](13-create-employee.md#step-144) · Next: [Step 146 — Edit Employee View](14-edit-employee.md#step-146)


---

<a id="step-146"></a>

### Step 146: Edit Employee View

**What was happening before this step?**

The edit form existed. The next question was how the page would find the right employee and save changes to that same account.

**What did we do, and what does it mean?**

The employee ID comes from the URL. The view finds that record, fills the form with its current values and, on submission, saves changes back to it. If the ID is invalid, it returns a “not found” response instead of editing someone else.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add these imports at the top.*

```python
from django.shortcuts import get_object_or_404
from .forms import EmployeeEditForm
```

*2. Then add this function at the bottom.*

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

**Understanding the code, one piece at a time**

The view receives an employee ID from the URL and finds that record. On GET, the form is filled with the current values. On POST, the same form is tied to the existing record through `instance=employee`; that is why Save updates the correct person instead of adding someone new.

**How did we know what happened?**

The corresponding template and URL are implemented in Steps 147–148.

**Where to go next:** Previous: [Step 145 — Edit Employee Form](14-edit-employee.md#step-145) · Next: [Step 147 — Edit Employee Template](14-edit-employee.md#step-147)


---

<a id="step-147"></a>

### Step 147: Edit Employee Template

**What was happening before this step?**

The edit view needed an actual page with fields and a save button.

**What did we do, and what does it mean?**

The edit template shows the existing values and any validation errors. The browser sees the form; the view remains responsible for reading and saving the data.

**File or place:** `templates/accounts/edit_employee.html`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

The URL and navigation are connected in the next steps.

**Where to go next:** Previous: [Step 146 — Edit Employee View](14-edit-employee.md#step-146) · Next: [Step 148 — Edit Employee URL](14-edit-employee.md#step-148)


---

<a id="step-148"></a>

### Step 148: Edit Employee URL

**What was happening before this step?**

The browser needed a unique address for editing each employee.

**What did we do, and what does it mean?**

The edit URL contains an employee ID. It tells the same edit view which record to load, so we do not need a different Python function for every person.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside your existing urlpatterns list.*

```python
path(
    'employees/edit/<int:employee_id>/',
    views.edit_employee,
    name='edit_employee'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

A link or selection mechanism must still provide the correct ID from the employee list.

**Where to go next:** Previous: [Step 147 — Edit Employee Template](14-edit-employee.md#step-147) · Next: [Step 149 — Employee Edit Link](14-edit-employee.md#step-149)


---

<a id="step-149"></a>

### Step 149: Employee Edit Link

**What was happening before this step?**

The list showed employees but did not yet let an administrator open one person's edit page.

**What did we do, and what does it mean?**

We added edit links using each row's employee ID. Clicking a link opens the edit form for that particular record.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Find the table headings and add.*

```html
<th>Actions</th>
```

*2. Place it immediately after.*

```html
<th>Status</th>
```

*3. Inside the {% for employee in employees %} loop, add this cell immediately after the employee's status </td>.*

```html
<td>
    <a href="{% url 'edit_employee' employee.id %}">
        Edit
    </a>
</td>
```

*4. Find the empty-table message.*

```html
<td colspan="5">No employees found.</td>
```

**How did we know what happened?**

The next steps explore a selection-based editing interface.

**Where to go next:** Previous: [Step 148 — Edit Employee URL](14-edit-employee.md#step-148) · Next: [Step 150 — Employee Edit Mode](14-edit-employee.md#step-150)


---

<a id="step-150"></a>

### Step 150: Employee Edit Mode

**What was happening before this step?**

A clickable employee row could make editing feel accidental. We wanted the action to be deliberate.

**What did we do, and what does it mean?**

We started introducing an edit mode instead of treating ordinary list browsing as an edit action. This is a user-interface improvement; the server-side permission checks still matter.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Find the employeelist() function and replace only its return render(...) statement with.*

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

**Understanding the code, one piece at a time**

An edit mode is a decision about the interface, not a new database table. The goal is to avoid people accidentally opening edit pages while they are only reading the list.

**How did we know what happened?**

The selection behavior is completed and corrected over Steps 151–156.

**Where to go next:** Previous: [Step 149 — Employee Edit Link](14-edit-employee.md#step-149) · Next: [Step 151 — Edit Mode Button](14-edit-employee.md#step-151)


---

<a id="step-151"></a>

### Step 151: Edit Mode Button

**What was happening before this step?**

We wanted a clear button that says when the administrator is entering edit mode.

**What did we do, and what does it mean?**

The page can change what controls are shown when Edit is selected. This helps avoid accidental clicks while someone is only trying to read employee details.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Immediately above the <table> tag, add.*

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

**How did we know what happened?**

The next step implements selecting a specific employee.

**Where to go next:** Previous: [Step 150 — Employee Edit Mode](14-edit-employee.md#step-150) · Next: [Step 152 — Select Employee](14-edit-employee.md#step-152)


---

<a id="step-152"></a>

### Step 152: Select Employee

**What was happening before this step?**

Once in edit mode, the administrator needed a way to choose which employee to edit.

**What did we do, and what does it mean?**

Selection controls on the list identify the desired row. The selected employee's ID is what ultimately tells the edit route which record to load.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Inside <head>, add.*

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

*2. Find the <tr> immediately after.*

```html
{% for employee in employees %}
```

*3. Replace that <tr> with.*

```html
<tr {% if edit_mode %}class="selectable"{% endif %}>
```

*4. Find the username cell.*

```html
<td>{{ employee.username }}</td>
```

*5. Replace it with.*

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

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

The fuller template version and a selection fix follow in Steps 153–156.

**Where to go next:** Previous: [Step 151 — Edit Mode Button](14-edit-employee.md#step-151) · Next: [Step 153 — Combine the list and selection controls](14-edit-employee.md#step-153)


---

<a id="step-153"></a>

### Step 153: Combine the list and selection controls

**What was happening before this step?**

The employee-list page had gathered several separate changes: employee data, an edit button and row selection.

**What did we do, and what does it mean?**

We combined these pieces in one template so they worked together. The final page should show the ordinary list clearly and reveal the editing controls only when needed.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Replace the entire contents of templates/accounts/employeelist.html with this.*

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

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

The selection behavior still needed the corrections noted in subsequent steps.

**Where to go next:** Previous: [Step 152 — Select Employee](14-edit-employee.md#step-152) · Next: [Step 154 — External CSS](14-edit-employee.md#step-154)


---

<a id="step-154"></a>

### Step 154: External CSS

**What was happening before this step?**

The HTML was becoming cluttered with styling instructions mixed into the page structure.

**What did we do, and what does it mean?**

We moved the appearance rules into a CSS file. CSS decides how a page looks; the HTML still describes its content and controls.

**File or place:** `accounts/static/accounts/style.css`.

**Code or command from this step**

*1. Add this to accounts/static/accounts/style.css.*

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

*2. Add this at the very beginning.*

```django
{% load static %}
```

*3. Replace the entire existing <head>...</head> section with.*

```html
<head>
    <title>Manage Employees</title>
    <link rel="stylesheet"
          href="{% static 'accounts/style.css' %}">
</head>
```

**Understanding the code, one piece at a time**

The CSS file holds rules such as spacing, colour and borders. The HTML file keeps the headings, table and buttons. Splitting them makes the code easier to find and understand later.

**How did we know what happened?**

Confirm the static file is loaded and the table retains its appearance.

**Where to go next:** Previous: [Step 153 — Combine the list and selection controls](14-edit-employee.md#step-153) · Next: [Step 155 — Fix Row Selection](14-edit-employee.md#step-155)


---

<a id="step-155"></a>

### Step 155: Fix Row Selection

**What was happening before this step?**

The first version of row selection did not behave correctly when the user clicked on an employee.

**What did we do, and what does it mean?**

We checked the connection between the clickable row, the selection control and the edit action. Fixing the page interaction did not change the employee database model.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Find.*

```html
{% for employee in employees %}
```

*2. Code or command used at this point.*

```html
<tr {% if edit_mode %}class="selectable"{% endif %}>
```

*3. Then replace the existing username <td>...</td> with.*

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

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

This is a bug-fix step, not a new permission rule.

**Where to go next:** Previous: [Step 154 — External CSS](14-edit-employee.md#step-154) · Next: [Step 156 — Correct employee row selection](14-edit-employee.md#step-156)


---

<a id="step-156"></a>

### Step 156: Correct employee row selection

**What was happening before this step?**

After finding the row-selection problem, we replaced the affected template code with a corrected version.

**What did we do, and what does it mean?**

The updated employee list keeps the same records and edit routes but changes how the selection interface works. This is a correction to an earlier step, not a separate employee-management feature.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Replace the entire file with.*

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

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

Test selecting different rows and confirm the correct employee opens in edit mode.

**Where to go next:** Previous: [Step 155 — Fix Row Selection](14-edit-employee.md#step-155) · Next: [Step 157 — Employee Deactivation](15-activate-deactivate-and-resend-setup-email.md#step-157)

---

### The point of this chapter

Editing is not creating a second employee. The view finds the existing account by its ID and saves new values to that same record. Later steps in this chapter fix the page's selection controls and move styling into CSS.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

