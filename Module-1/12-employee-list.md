# MODULE 1 — Accounts

## Chapter 12: Employee list (Steps 136–138)

**Where we are.** Administrators needed an application page listing employees, instead of relying only on Django Admin.

**What this chapter gives us.** Connect a restricted list view to an HTML table and URL.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain how a queryset becomes rows in a template.

### Find a step

- [Step 136: Employee Management](#step-136)
- [Step 137: Employee List Template](#step-137)
- [Step 138: Employee List URL](#step-138)

---

<a id="step-136"></a>

### Step 136: Employee Management

**Why we are doing this.** Account onboarding had working pieces, but administrators needed a single place to see and manage employee records.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these imports.*

```python
from .models import CustomUser
from .decorators import role_required
```

*2. Recorded snippet; Then add this function at the bottom.*

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

**How it works.** The employee-list view fetches `CustomUser` rows for display and is protected by the appropriate role check. It gives a foundation for add, edit, deactivate, and resend actions.

**Follow the flow.** The view queries the user model for employee records and passes them to the template as context. The template cannot invent records; it only displays the queryset it receives. A role decorator makes sure only the permitted administrator can access the list endpoint.

**How we checked it.** The HTML table and URL are added in Steps 137–138.

**Connects to.** Previous: [Step 135 — Resend Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-135) · Next: [Step 137 — Employee List Template](12-employee-list.md#step-137)


---

<a id="step-137"></a>

### Step 137: Employee List Template

**Why we are doing this.** The employee-list view supplies data, but a browser needs a page that shows the people and their current roles or status.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** The template loops through employee records and renders their fields in a table. It is an initial version; edit controls and styling are added later.

**Follow the flow.** The view passes a collection of employees into the HTML template. Django's `{% for %}` loop repeats a row for each employee and `{{ ... }}` outputs the relevant fields. This provides a read-only management overview before add/edit/status buttons are introduced.

**How we checked it.** The page becomes reachable when the list URL is configured.

**Connects to.** Previous: [Step 136 — Employee Management](12-employee-list.md#step-136) · Next: [Step 138 — Employee List URL](12-employee-list.md#step-138)


---

<a id="step-138"></a>

### Step 138: Employee List URL

**Why we are doing this.** The employee list had a view and template but no dedicated named route.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside your existing urlpatterns list.*

```python
path(
    'employees/',
    views.employee_list,
    name='employee_list'
),
```

**How it works.** `path(...)` maps the employee-list URL to the view. Other templates can refer to this route by name when linking back after changes.

**How we checked it.** The create-employee workflow begins next.

**Connects to.** Previous: [Step 137 — Employee List Template](12-employee-list.md#step-137) · Next: [Step 139 — Create Employee View](13-create-employee.md#step-139)

---

**Chapter recap.** Connect a restricted list view to an HTML table and URL.

**Practice explaining:** Explain how a queryset becomes rows in a template.

