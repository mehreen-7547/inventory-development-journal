# MODULE 1 — Accounts

## Chapter 18: Shared base template and page styling (Steps 179–194)

**Where we are.** Account pages were functionally built but duplicated layout and inconsistent form styling. We consolidated the visual structure and guarded against mail failures.

**What this chapter gives us.** Use base.html inheritance, Bootstrap widget classes, and explicit error handling after saving employee records.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain template inheritance and why SMTP failure must not roll back a successfully saved employee without a deliberate transaction policy.

### Find a step

- [Step 179: Dashboard Template Inheritance](#step-179)
- [Step 180: Update Login Page](#step-180)
- [Step 181: Update Change Password Page](#step-181)
- [Step 182: Update Forgot Password Page](#step-182)
- [Step 183: Update Reset Confirmation](#step-183)
- [Step 184: Update Set New Password Page](#step-184)
- [Step 185: Update Reset Complete Page](#step-185)
- [Step 186: Update Employee List](#step-186)
- [Step 187: Update Create Employee](#step-187)
- [Step 188: Fix Form Styling](#step-188)
- [Step 189: Add CSS classes to creation form widgets](#step-189)
- [Step 190: Correct Form Styling](#step-190)
- [Step 191: Consolidate both employee forms](#step-191)
- [Step 192: Update Edit Employee](#step-192)
- [Step 193: Email Error Handling](#step-193)
- [Step 194: Resend Email Error Handling](#step-194)

---

<a id="step-179"></a>

### Step 179: Dashboard Template Inheritance

**Why we are doing this.** The homepage needed to use the new shared layout instead of keeping its own duplicate full HTML structure.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Dashboard - Inventory System{% endblock %}

{% block content %}

<h1>Inventory &amp; Demand Intelligence</h1>

<!-- WELCOME -->
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>

<!-- DASHBOARD -->
{% if request.user.role == 'admin' %}

    <h3>Administrator Dashboard</h3>

    {% if request.user.is_staff %}
        <p>
            <a href="/admin/">Open Django admin</a>
        </p>
    {% endif %}

{% elif request.user.role == 'manager' %}

    <h3>Inventory Manager Dashboard</h3>

{% elif request.user.role == 'sales' %}

    <h3>Sales Staff Dashboard</h3>

{% endif %}

{% endblock %}
```

*2. Recorded snippet; Run the Django server if it isn't already running.*

```powershell
python manage.py runserver
```

**How it works.** `{% extends 'base.html' %}` adopts the parent layout; named blocks fill in title and page content. This keeps navigation in one place.

**Follow the flow.** The child template begins with `{% extends 'base.html' %}`. Its `{% block title %}` supplies the page title, and `{% block content %}` supplies homepage-specific markup. Django combines parent and child when rendering. The role conditions still choose dashboard labels as before; only the layout structure changes.

**How we checked it.** Run the server and confirm homepage content appears inside the shared shell.

**Connects to.** Previous: [Step 178 — Create the shared base.html template](17-real-email-delivery-smtp.md#step-178) · Next: [Step 180 — Update Login Page](18-shared-base-template-and-page-styling.md#step-180)


---

<a id="step-180"></a>

### Step 180: Update Login Page

**Why we are doing this.** The login page was still using its original standalone HTML, so it looked different from the rest of the application.

**File / location.** `templates/accounts/login.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Login - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-4">

        <h2 class="mb-4">Login</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                <label for="username" class="form-label">
                    Username
                </label>
                <input type="text"
                       id="username"
                       name="username"
                       class="form-control"
                       placeholder="Enter username"
                       autocomplete="username"
                       required>
            </div>

            <div class="mb-3">
                <label for="password" class="form-label">
                    Password
                </label>
                <input type="password"
                       id="password"
                       name="password"
                       class="form-control"
                       placeholder="Enter password"
                       autocomplete="current-password"
                       required>
            </div>

            <button type="submit" class="btn btn-primary w-100">
                Login
            </button>
        </form>

        <p class="mt-3 text-center">
            <a href="{% url 'password_reset' %}">
                Forgot Password?
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

**How it works.** Replacing the template with a child of `base.html` standardizes form spacing and adds navigation to password recovery. The login view and credentials logic stay the same.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Test an invalid login and a valid login; both should still behave as before.

**Connects to.** Previous: [Step 179 — Dashboard Template Inheritance](18-shared-base-template-and-page-styling.md#step-179) · Next: [Step 181 — Update Change Password Page](18-shared-base-template-and-page-styling.md#step-181)


---

<a id="step-181"></a>

### Step 181: Update Change Password Page

**Why we are doing this.** The change-password form should share the application's layout while preserving its security behavior.

**File / location.** `templates/accounts/change_password.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Change Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-4">Change Password</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Change Password
            </button>

            <a href="{% url 'home' %}"
               class="btn btn-outline-secondary">
                Cancel
            </a>
        </form>

    </div>
</div>

{% endblock %}
```

**How it works.** The child template defines page-specific form markup within shared blocks and uses Bootstrap classes for consistent spacing. Password validation remains in Django's form.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Confirm old-password errors and successful changes still display correctly.

**Connects to.** Previous: [Step 180 — Update Login Page](18-shared-base-template-and-page-styling.md#step-180) · Next: [Step 182 — Update Forgot Password Page](18-shared-base-template-and-page-styling.md#step-182)


---

<a id="step-182"></a>

### Step 182: Update Forgot Password Page

**Why we are doing this.** The forgot-password screen also needed to match the new layout and explain its purpose clearly.

**File / location.** `templates/accounts/password_reset.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace its entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Forgot Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-3">Forgot Password</h2>

        <p>
            Enter your registered email address
            to reset your password.
        </p>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Send Reset Link
            </button>
        </form>

        <p class="mt-3">
            <a href="{% url 'login' %}">
                Back to Login
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

**How it works.** The template extends `base.html`, renders the email form, and links the user through the existing recovery route. It does not change token generation.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Submit a test address in console mode and check the expected confirmation screen.

**Connects to.** Previous: [Step 181 — Update Change Password Page](18-shared-base-template-and-page-styling.md#step-181) · Next: [Step 183 — Update Reset Confirmation](18-shared-base-template-and-page-styling.md#step-183)


---

<a id="step-183"></a>

### Step 183: Update Reset Confirmation

**Why we are doing this.** After requesting a reset email, the confirmation screen should use the same navigation and typography as the form.

**File / location.** `templates/accounts/password_reset_done.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Password Reset Requested - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-6">

        <h2 class="mb-4">Check Your Email</h2>

        <div class="alert alert-info">
            If an active account exists with that email address,
            password reset instructions will be sent.
        </div>

        <p>
            Please check your email for the reset link.
        </p>

        <a href="{% url 'login' %}"
           class="btn btn-outline-primary mt-2">
            Back to Login
        </a>

    </div>
</div>

{% endblock %}
```

**How it works.** This child template replaces its old standalone HTML while keeping the same neutral wording about the email request.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** The existing reset flow should still reach the confirmation screen.

**Connects to.** Previous: [Step 182 — Update Forgot Password Page](18-shared-base-template-and-page-styling.md#step-182) · Next: [Step 184 — Update Set New Password Page](18-shared-base-template-and-page-styling.md#step-184)


---

<a id="step-184"></a>

### Step 184: Update Set New Password Page

**Why we are doing this.** The token-link destination is especially important because users arrive there from email, not the app's normal navigation.

**File / location.** `templates/accounts/password_reset_confirm.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Set New Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-5">

        {% if validlink %}

            <h2 class="mb-4">Set New Password</h2>

            <form method="POST">
                {% csrf_token %}

                <div class="mb-3">
                    {{ form.as_p }}
                </div>

                <button type="submit" class="btn btn-primary">
                    Reset Password
                </button>
            </form>

        {% else %}

            <h2 class="mb-4">Invalid Reset Link</h2>

            <div class="alert alert-warning">
                This password reset link is invalid or has
                expired. Please request a new one.
            </div>

            <a href="{% url 'password_reset' %}"
               class="btn btn-outline-primary">
                Request New Link
            </a>

        {% endif %}

    </div>
</div>

{% endblock %}
```

**How it works.** The new template extends `base.html`, shows password fields or an invalid-link message as appropriate, and preserves CSRF protection.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Test with a valid token and an expired/invalid link; no separate automated output is provided in the notes.

**Connects to.** Previous: [Step 183 — Update Reset Confirmation](18-shared-base-template-and-page-styling.md#step-183) · Next: [Step 185 — Update Reset Complete Page](18-shared-base-template-and-page-styling.md#step-185)


---

<a id="step-185"></a>

### Step 185: Update Reset Complete Page

**Why we are doing this.** The final password-reset success page needed to close the workflow clearly and offer a route back to login.

**File / location.** `templates/accounts/password_reset_complete.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Password Reset Complete - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-5">

        <h2 class="mb-4">Password Reset Successful</h2>

        <div class="alert alert-success">
            Your password has been reset successfully.
            You can now log in using your new password.
        </div>

        <a href="{% url 'login' %}"
           class="btn btn-primary">
            Go to Login
        </a>

    </div>
</div>

{% endblock %}
```

**How it works.** The template uses the base layout and completion message. It is reached only after Django accepts the reset form.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** A successful reset should arrive here, and the new password should work at login.

**Connects to.** Previous: [Step 184 — Update Set New Password Page](18-shared-base-template-and-page-styling.md#step-184) · Next: [Step 186 — Update Employee List](18-shared-base-template-and-page-styling.md#step-186)


---

<a id="step-186"></a>

### Step 186: Update Employee List

**Why we are doing this.** Employee management is a central admin screen, so it needed the same shared layout and more readable table/actions.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}
{% load static %}

{% block title %}Manage Employees - Inventory System{% endblock %}

{% block extra_css %}
<link rel="stylesheet" href="{% static 'accounts/style.css' %}">
{% endblock %}

{% block content %}

<h2 class="mb-4">Manage Employees</h2>

<div class="d-flex gap-2 mb-4">

    <a href="{% url 'create_employee' %}"
       class="btn btn-primary">
        Add New Employee
    </a>

    {% if edit_mode %}

        <a href="{% url 'employee_list' %}"
           class="btn btn-outline-secondary">
            Cancel Edit
        </a>

    {% else %}

        <a href="{% url 'employee_list' %}?mode=edit"
           class="btn btn-outline-primary">
            Edit Employee
        </a>

    {% endif %}

</div>

{% if edit_mode %}
    <p class="text-muted">
        Select an employee to edit.
    </p>
{% endif %}

<div class="table-responsive">

    <table class="table table-bordered table-hover align-middle">

        <thead class="table-light">
            <tr>
                <th>Username</th>
                <th>Name</th>
                <th>Email</th>
                <th>Role</th>
                <th>Status</th>
            </tr>
        </thead>

        <tbody>

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
                        <span class="badge bg-success">Active</span>
                    {% else %}
                        <span class="badge bg-secondary">Inactive</span>
                    {% endif %}
                </td>

            </tr>

            {% empty %}

            <tr>
                <td colspan="5" class="text-center">
                    No employees found.
                </td>
            </tr>

            {% endfor %}

        </tbody>

    </table>

</div>

<a href="{% url 'home' %}"
   class="btn btn-outline-secondary mt-3">
    Back to Home
</a>

{% endblock %}
```

**How it works.** The replacement template extends `base.html`, presents employees in a Bootstrap table, and retains action links/forms. It supersedes the earlier standalone list markup.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** Recheck edit selection, activation, and resend actions after the visual update.

**Connects to.** Previous: [Step 185 — Update Reset Complete Page](18-shared-base-template-and-page-styling.md#step-185) · Next: [Step 187 — Update Create Employee](18-shared-base-template-and-page-styling.md#step-187)


---

<a id="step-187"></a>

### Step 187: Update Create Employee

**Why we are doing this.** The create-employee screen needed matching styling so its required fields and feedback were easy to understand.

**File / location.** `templates/accounts/create_employee.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Create Employee - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-8 col-lg-6">

        <h2 class="mb-3">Create Employee</h2>

        <p class="text-muted mb-4">
            Enter the employee details below.
            A password setup link will be sent to their email.
        </p>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-4">
                {{ form.as_p }}
            </div>

            <div class="d-flex gap-2">
                <button type="submit" class="btn btn-primary">
                    Create Employee
                </button>

                <a href="{% url 'employee_list' %}"
                   class="btn btn-outline-secondary">
                    Cancel
                </a>
            </div>
        </form>

    </div>
</div>

{% endblock %}
```

**How it works.** The new child template organizes form inputs and buttons within the shared layout. It still submits to the create view with a CSRF token.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Verify role selection and employee creation still work; input styling is refined next.

**Connects to.** Previous: [Step 186 — Update Employee List](18-shared-base-template-and-page-styling.md#step-186) · Next: [Step 188 — Fix Form Styling](18-shared-base-template-and-page-styling.md#step-188)


---

<a id="step-188"></a>

### Step 188: Fix Form Styling

**Why we are doing this.** The updated page layout looked correct, but `form.as_p` still produced default-looking HTML controls.

**File / location.** `accounts/forms.py (widget styling)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this code at the end of that method, after the role choices.*

```python
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

**How it works.** Bootstrap requires classes on the rendered input widgets; changing surrounding HTML alone cannot style every Django form field.

**How we checked it.** The next steps add the missing classes inside the form's initialization code.

**Connects to.** Previous: [Step 187 — Update Create Employee](18-shared-base-template-and-page-styling.md#step-187) · Next: [Step 189 — Add CSS classes to creation form widgets](18-shared-base-template-and-page-styling.md#step-189)


---

<a id="step-189"></a>

### Step 189: Add CSS classes to creation form widgets

**Why we are doing this.** The create form needed to apply CSS classes to each generated field while preserving special handling for the role dropdown.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        # Bootstrap styling
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

**How it works.** Inside `EmployeeCreationForm.__init__`, iterating over `self.fields` updates widget attributes. The form controls validation; the widget attributes control appearance.

**Follow the flow.** The form's `self.fields` dictionary contains each bound Django field. We iterate over the fields and set each widget's `class` attribute: `form-select` for a dropdown and `form-control` for ordinary inputs. This changes rendered HTML styling, not the database fields or the form's validation rules.

**How we checked it.** The source's next correction indicates this change was initially put in the wrong form class.

**Connects to.** Previous: [Step 188 — Fix Form Styling](18-shared-base-template-and-page-styling.md#step-188) · Next: [Step 190 — Correct Form Styling](18-shared-base-template-and-page-styling.md#step-190)


---

<a id="step-190"></a>

### Step 190: Correct Form Styling

**Why we are doing this.** The styling code had accidentally been added to `EmployeeEditForm` rather than to `EmployeeCreationForm`.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find EmployeeCreationForm and replace only its init() method with.*

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

**How it works.** The fix places widget-class logic in the create form as well. The edit form can keep the styling because both pages benefit from it.

**How we checked it.** This is a correction of placement, not an extra form feature.

**Connects to.** Previous: [Step 189 — Add CSS classes to creation form widgets](18-shared-base-template-and-page-styling.md#step-189) · Next: [Step 191 — Consolidate both employee forms](18-shared-base-template-and-page-styling.md#step-191)


---

<a id="step-191"></a>

### Step 191: Consolidate both employee forms

**Why we are doing this.** After several partial form edits, a complete `accounts/forms.py` version reduced the risk of inconsistent definitions.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire file with this code. Both Create Employee and Edit Employee will have Bootstrap-styled fields.*

```python
from django import forms
from .models import CustomUser


class EmployeeCreationForm(forms.ModelForm):

    class Meta:
        model = CustomUser
        fields = ['username', 'first_name', 'last_name', 'email', 'role']

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'

    def save(self, commit=True):
        user = super().save(commit=False)
        user.set_unusable_password()

        if commit:
            user.save()

        return user


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

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

**How it works.** The replacement file contains both creation and editing forms with role restrictions and Bootstrap widget classes. Read it as the latest version of the forms at this point.

**Follow the flow.** The complete file now contains both `EmployeeCreationForm` and `EmployeeEditForm`. Each specifies its allowed model fields and Bootstrap widget styling. The creation form still sets an unusable initial password; the edit form operates on an existing user instance. Consolidating both versions makes the final form behavior easier to inspect.

**How we checked it.** Refresh both create and edit pages; field styling should be consistent and validation preserved.

**Connects to.** Previous: [Step 190 — Correct Form Styling](18-shared-base-template-and-page-styling.md#step-190) · Next: [Step 192 — Update Edit Employee](18-shared-base-template-and-page-styling.md#step-192)


---

<a id="step-192"></a>

### Step 192: Update Edit Employee

**Why we are doing this.** The edit-employee page was the remaining major account form still using the older standalone layout.

**File / location.** `templates/accounts/edit_employee.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace the entire contents with.*

```html
{% extends 'base.html' %}

{% block title %}Edit Employee - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-8 col-lg-6">

        <h2 class="mb-3">Edit Employee</h2>

        <p class="text-muted">
            Username: <strong>{{ employee.username }}</strong>
        </p>

        <!-- EDIT EMPLOYEE DETAILS -->
        <form method="POST" class="mb-4">
            {% csrf_token %}

            <div class="mb-4">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Save Changes
            </button>
        </form>

        <hr>

        <!-- EMPLOYEE STATUS -->
        <h5 class="mt-4">Account Status</h5>

        <p>
            Current status:
            {% if employee.is_active %}
                <span class="badge bg-success">Active</span>
            {% else %}
                <span class="badge bg-secondary">Inactive</span>
            {% endif %}
        </p>

        <form method="POST"
              action="{% url 'toggle_employee_status' employee.id %}"
              class="mb-4">

            {% csrf_token %}

            {% if employee.is_active %}
                <button type="submit" class="btn btn-outline-danger">
                    Deactivate Employee
                </button>
            {% else %}
                <button type="submit" class="btn btn-outline-success">
                    Reactivate Employee
                </button>
            {% endif %}

        </form>

        <!-- PASSWORD SETUP -->
        {% if employee.is_active and not employee.has_usable_password %}

            <hr>

            <h5 class="mt-4">Password Setup</h5>

            <p class="text-muted">
                This employee has not set a password yet.
            </p>

            <form method="POST"
                  action="{% url 'resend_setup_email' employee.id %}"
                  class="mb-4">

                {% csrf_token %}

                <button type="submit" class="btn btn-outline-primary">
                    Resend Setup Email
                </button>

            </form>

        {% endif %}

        <hr>

        <a href="{% url 'employee_list' %}"
           class="btn btn-outline-secondary mt-2">
            Back to Manage Employees
        </a>

    </div>
</div>

{% endblock %}
```

**How it works.** The replacement template extends `base.html`, uses Bootstrap classes, renders errors, and keeps the existing edit submission route.

The token helps Django reject forged state-changing form submissions.

Template inheritance reuses the common page layout; only named blocks need page-specific markup.

**How we checked it.** Verify an authorized edit saves correctly and a malformed submission returns understandable errors.

**Connects to.** Previous: [Step 191 — Consolidate both employee forms](18-shared-base-template-and-page-styling.md#step-191) · Next: [Step 193 — Email Error Handling](18-shared-base-template-and-page-styling.md#step-193)


---

<a id="step-193"></a>

### Step 193: Email Error Handling

**Why we are doing this.** A subtle failure remained: an account could be saved successfully, but a later SMTP error might cause the page to crash and mislead the administrator.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; A. Add this import at the top of views.py.*

```python
from smtplib import SMTPException
```

*2. Recorded snippet; B. Inside createemployee(), find.*

```python
employee = form.save()
send_welcome_email(request, employee)
messages.success(request, 'Employee created successfully.')
return redirect('employee_list')
```

*3. Recorded snippet; Replace those lines with.*

```python
            employee = form.save()

            try:
                send_welcome_email(request, employee)

            except (SMTPException, OSError):
                messages.error(
                    request,
                    'Employee created, but the setup email could not '
                    'be sent. Use Resend Setup Email to try again.'
                )

            else:
                messages.success(
                    request,
                    'Employee created successfully.'
                )

            return redirect('employee_list')
```

*4. Recorded snippet; Save the file and run.*

```powershell
python manage.py check
```

**How it works.** Catching specific email exceptions around the welcome-message call lets the view show a warning while preserving the already-created employee. The database save and email delivery are separate operations.

**Follow the flow.** The employee is saved *before* Django attempts the welcome email. `try` calls the sending helper; `except (SMTPException, OSError)` catches common mail and network failures and creates a warning message. The `else` clause runs only if sending raises no caught exception. In either case the view redirects to the list. This avoids reporting a failed account creation when only email delivery failed.

**How we checked it.** A failure-injection or temporarily misconfigured SMTP test would confirm the warning path; no such completed test is recorded here.

**Connects to.** Previous: [Step 192 — Update Edit Employee](18-shared-base-template-and-page-styling.md#step-192) · Next: [Step 194 — Resend Email Error Handling](18-shared-base-template-and-page-styling.md#step-194)


---

<a id="step-194"></a>

### Step 194: Resend Email Error Handling

**Why we are doing this.** The resend action had the same risk as initial welcome delivery: a mail-server failure should not crash the employee page.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find these lines inside resendsetupemail().*

```python
    else:
        send_welcome_email(request, employee)
        messages.success(request, 'Password setup email generated successfully.')
```

*2. Recorded snippet; Replace them with.*

```python
    else:
        try:
            send_welcome_email(request, employee)

        except (SMTPException, OSError):
            messages.error(
                request,
                'Setup email could not be sent. Please try again.'
            )

        else:
            messages.success(
                request,
                'Password setup email generated successfully.'
            )
```

*3. Recorded snippet; Save views.py, then run.*

```powershell
python manage.py check
```

**How it works.** The resend view catches delivery errors and uses Django messages to report them. It leaves the existing employee account unchanged when delivery fails.

**Follow the flow.** The resend endpoint does not create or modify the employee record. It attempts another email delivery. If mail/network errors occur, the `except` block shows a retry message; otherwise the `else` block shows success. This mirrors the initial welcome-email handling and avoids a server error page for a routine delivery problem.

**How we checked it.** The source includes follow-up checks and analysis, but not a verified end-to-end failure test for every SMTP exception.

**Connects to.** Previous: [Step 193 — Email Error Handling](18-shared-base-template-and-page-styling.md#step-193)

---

**Chapter recap.** Use base.html inheritance, Bootstrap widget classes, and explicit error handling after saving employee records.

**Practice explaining:** Explain template inheritance and why SMTP failure must not roll back a successfully saved employee without a deliberate transaction policy.

