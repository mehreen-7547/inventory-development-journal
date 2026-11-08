# MODULE 1 — Employee accounts

## Chapter 18: Shared base template and page styling (Steps 179–194)

### Before we begin

The login and employee pages had grown one by one. They worked, but many contained repeated headers, menus and form styling.

### What we worked on

We built a shared page layout, updated each account screen to use it and corrected where form styling belonged. At the end, we also handled a case where an employee account saves successfully but its welcome email fails.

### Picture it this way

The shared `base.html` template is a page frame reused everywhere. Each child page fills in only its own content. The last two steps are about making the app explain email failures accurately, not about changing the shared layout.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

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

**What was happening before this step?**

Now that the common layout existed, the homepage needed to use it instead of repeating all its own HTML.

**What did we do, and what does it mean?**

The homepage template extends `base.html` and supplies its own heading and content inside named sections. Django combines both files into the final page shown in the browser.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

*2. Run the Django server if it isn't already running.*

```powershell
python manage.py runserver
```

**Understanding the code, one piece at a time**

`{% extends 'base.html' %}` tells Django to start with the common page frame. `{% block content %}` says what belongs in the homepage's own section. Django joins the two when returning the HTML to the browser.

**How did we know what happened?**

Run the server and confirm homepage content appears inside the shared shell.

**Where to go next:** Previous: [Step 178 — Create the shared base.html template](17-real-email-delivery-smtp.md#step-178) · Next: [Step 180 — Update Login Page](18-shared-base-template-and-page-styling.md#step-180)


---

<a id="step-180"></a>

### Step 180: Update Login Page

**What was happening before this step?**

The login page still had its old standalone layout and looked different from the rest of the website.

**What did we do, and what does it mean?**

We connected it to the shared `base.html` layout. The username/password form and login check remain the same; only the surrounding page structure and appearance changed.

**File or place:** `templates/accounts/login.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Test an invalid login and a valid login; both should still behave as before.

**Where to go next:** Previous: [Step 179 — Dashboard Template Inheritance](18-shared-base-template-and-page-styling.md#step-179) · Next: [Step 181 — Update Change Password Page](18-shared-base-template-and-page-styling.md#step-181)


---

<a id="step-181"></a>

### Step 181: Update Change Password Page

**What was happening before this step?**

We wanted the change-password page to look and behave like part of the same application.

**What did we do, and what does it mean?**

The template now uses the shared layout, while its form still asks for old and new passwords. Moving HTML into the common design does not remove the password checks in the view.

**File or place:** `templates/accounts/change_password.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Confirm old-password errors and successful changes still display correctly.

**Where to go next:** Previous: [Step 180 — Update Login Page](18-shared-base-template-and-page-styling.md#step-180) · Next: [Step 182 — Update Forgot Password Page](18-shared-base-template-and-page-styling.md#step-182)


---

<a id="step-182"></a>

### Step 182: Update Forgot Password Page

**What was happening before this step?**

The forgot-password screen was another page that needed the shared header and consistent spacing.

**What did we do, and what does it mean?**

We changed its HTML to reuse the common layout. The purpose of the page remains the same: ask for the email address associated with an account so Django can prepare a reset link.

**File or place:** `templates/accounts/password_reset.html`.

**Code or command from this step**

*1. Replace its entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Submit a test address in console mode and check the expected confirmation screen.

**Where to go next:** Previous: [Step 181 — Update Change Password Page](18-shared-base-template-and-page-styling.md#step-181) · Next: [Step 183 — Update Reset Confirmation](18-shared-base-template-and-page-styling.md#step-183)


---

<a id="step-183"></a>

### Step 183: Update Reset Confirmation

**What was happening before this step?**

The “check your email” screen appeared after a password-reset request, and it also needed the new layout.

**What did we do, and what does it mean?**

We kept the neutral confirmation message but placed it inside the shared page frame. The page is meant to reassure the requester without revealing whether that email address exists.

**File or place:** `templates/accounts/password_reset_done.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% extends ... %}` means this page reuses the common layout file. The page only supplies its own content for the named sections.

**How did we know what happened?**

The existing reset flow should still reach the confirmation screen.

**Where to go next:** Previous: [Step 182 — Update Forgot Password Page](18-shared-base-template-and-page-styling.md#step-182) · Next: [Step 184 — Update Set New Password Page](18-shared-base-template-and-page-styling.md#step-184)


---

<a id="step-184"></a>

### Step 184: Update Set New Password Page

**What was happening before this step?**

The password-reset link opens a page outside the normal app navigation, so the recipient should immediately understand what to do.

**What did we do, and what does it mean?**

The reset-confirm template uses the same layout and clearly shows the new-password form or an invalid-link message. Django still decides whether the token can be used; HTML styling does not make the link valid.

**File or place:** `templates/accounts/password_reset_confirm.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Test with a valid token and an expired/invalid link; no separate automated output is provided in the notes.

**Where to go next:** Previous: [Step 183 — Update Reset Confirmation](18-shared-base-template-and-page-styling.md#step-183) · Next: [Step 185 — Update Reset Complete Page](18-shared-base-template-and-page-styling.md#step-185)


---

<a id="step-185"></a>

### Step 185: Update Reset Complete Page

**What was happening before this step?**

After someone has successfully reset a password, the last screen should make the next action obvious.

**What did we do, and what does it mean?**

We updated the completion page to match the rest of the site and guide the person back to login. No password details need to appear there.

**File or place:** `templates/accounts/password_reset_complete.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% extends ... %}` means this page reuses the common layout file. The page only supplies its own content for the named sections.

**How did we know what happened?**

A successful reset should arrive here, and the new password should work at login.

**Where to go next:** Previous: [Step 184 — Update Set New Password Page](18-shared-base-template-and-page-styling.md#step-184) · Next: [Step 186 — Update Employee List](18-shared-base-template-and-page-styling.md#step-186)


---

<a id="step-186"></a>

### Step 186: Update Employee List

**What was happening before this step?**

The employee list had become the administrator's main account-management screen, so its table and buttons needed a clearer layout.

**What did we do, and what does it mean?**

We reused the shared template while keeping the employee rows and management actions together. The goal was to make it easy to find a person and understand which action each button performs.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% extends ... %}` means this page reuses the common layout file. The page only supplies its own content for the named sections.

**How did we know what happened?**

Recheck edit selection, activation, and resend actions after the visual update.

**Where to go next:** Previous: [Step 185 — Update Reset Complete Page](18-shared-base-template-and-page-styling.md#step-185) · Next: [Step 187 — Update Create Employee](18-shared-base-template-and-page-styling.md#step-187)


---

<a id="step-187"></a>

### Step 187: Update Create Employee

**What was happening before this step?**

The form for creating a new employee needed to look consistent with the newly styled employee list.

**What did we do, and what does it mean?**

We moved it into the common page layout and kept a clear form and save action. The server still validates the information before creating a user.

**File or place:** `templates/accounts/create_employee.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Verify role selection and employee creation still work; input styling is refined next.

**Where to go next:** Previous: [Step 186 — Update Employee List](18-shared-base-template-and-page-styling.md#step-186) · Next: [Step 188 — Fix Form Styling](18-shared-base-template-and-page-styling.md#step-188)


---

<a id="step-188"></a>

### Step 188: Fix Form Styling

**What was happening before this step?**

The pages looked more consistent, but the boxes and dropdowns generated by Django forms still looked plain.

**What did we do, and what does it mean?**

Django creates HTML form controls from its Python form fields. We needed to add styling instructions to those controls, not only to the page around them.

**File or place:** `accounts/forms.py (widget styling)`.

**Code or command from this step**

*1. Add this code at the end of that method, after the role choices.*

```python
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

**Understanding the code, one piece at a time**

CSS classes are labels attached to HTML elements. A stylesheet uses those labels to decide how the input boxes and dropdowns look. Adding a class does not alter which database field a form writes to.

**How did we know what happened?**

The next steps add the missing classes inside the form's initialization code.

**Where to go next:** Previous: [Step 187 — Update Create Employee](18-shared-base-template-and-page-styling.md#step-187) · Next: [Step 189 — Add CSS classes to creation form widgets](18-shared-base-template-and-page-styling.md#step-189)


---

<a id="step-189"></a>

### Step 189: Add CSS classes to creation form widgets

**What was happening before this step?**

The employee creation form had text fields and a role dropdown. We wanted both to use the correct styles without changing what they mean.

**What did we do, and what does it mean?**

When Django builds the form, a small method adds a CSS class to each field. Text inputs get one class and the role dropdown another. This changes appearance, not which roles are allowed or how values are saved.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Code or command used at this point.*

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

**Understanding the code, one piece at a time**

Inside `__init__`, the form loops over the fields Django created and gives each field's HTML widget an appropriate CSS class. The role dropdown is treated differently from ordinary text inputs because it is a select box.

**How did we know what happened?**

The source's next correction indicates this change was initially put in the wrong form class.

**Where to go next:** Previous: [Step 188 — Fix Form Styling](18-shared-base-template-and-page-styling.md#step-188) · Next: [Step 190 — Correct Form Styling](18-shared-base-template-and-page-styling.md#step-190)


---

<a id="step-190"></a>

### Step 190: Correct Form Styling

**What was happening before this step?**

We noticed the styling instructions were added to the wrong form in an earlier edit.

**What did we do, and what does it mean?**

The project had a creation form and an edit form, and they are different Python classes. We corrected which class received the styling method, so the intended creation page would actually use it.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Find EmployeeCreationForm and replace only its init() method with.*

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

**Understanding the code, one piece at a time**

The project has both an employee creation form and an employee edit form. Code added to one class does not magically affect the other. We moved the styling to the intended class.

**How did we know what happened?**

This is a correction of placement, not an extra form feature.

**Where to go next:** Previous: [Step 189 — Add CSS classes to creation form widgets](18-shared-base-template-and-page-styling.md#step-189) · Next: [Step 191 — Consolidate both employee forms](18-shared-base-template-and-page-styling.md#step-191)


---

<a id="step-191"></a>

### Step 191: Consolidate both employee forms

**What was happening before this step?**

After several small corrections, the complete form file was easier to understand than a patchwork of partial edits.

**What did we do, and what does it mean?**

We brought the creation and edit form definitions together in one final version. This makes it clearer which fields create a new account, which fields edit an existing one, and where the styling is applied.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Replace the entire file with this code. Both Create Employee and Edit Employee will have Bootstrap-styled fields.*

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

**Understanding the code, one piece at a time**

The final forms file contains the current versions together. It is useful to compare them: creation makes a new user without a usable password; editing changes an existing record. Both may share styling, but they have different purposes.

**How did we know what happened?**

Refresh both create and edit pages; field styling should be consistent and validation preserved.

**Where to go next:** Previous: [Step 190 — Correct Form Styling](18-shared-base-template-and-page-styling.md#step-190) · Next: [Step 192 — Update Edit Employee](18-shared-base-template-and-page-styling.md#step-192)


---

<a id="step-192"></a>

### Step 192: Update Edit Employee

**What was happening before this step?**

The edit-employee page was one of the remaining screens using its older layout.

**What did we do, and what does it mean?**

We updated its HTML to reuse the common page frame, while keeping the employee's current values, save button and any status or resend actions. The actual update still happens in the view.

**File or place:** `templates/accounts/edit_employee.html`.

**Code or command from this step**

*1. Replace the entire contents with.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Verify an authorized edit saves correctly and a malformed submission returns understandable errors.

**Where to go next:** Previous: [Step 191 — Consolidate both employee forms](18-shared-base-template-and-page-styling.md#step-191) · Next: [Step 193 — Email Error Handling](18-shared-base-template-and-page-styling.md#step-193)


---

<a id="step-193"></a>

### Step 193: Email Error Handling

**What was happening before this step?**

A tricky problem remained: an administrator could save a new employee successfully, but Gmail might fail a moment later. The page could then show an error, making it look as though the account was never created.

**What did we do, and what does it mean?**

We separated those two outcomes. First, the employee is saved. Then the code tries to send the welcome email. If sending fails, the page shows a warning about the email rather than pretending the whole employee creation failed.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. A. Add this import at the top of views.py.*

```python
from smtplib import SMTPException
```

*2. B. Inside createemployee(), find.*

```python
employee = form.save()
send_welcome_email(request, employee)
messages.success(request, 'Employee created successfully.')
return redirect('employee_list')
```

*3. Replace those lines with.*

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

*4. Save the file and run.*

```powershell
python manage.py check
```

**Understanding the code, one piece at a time**

`try` means “attempt to send the email.” `except` catches the kinds of failures we expect from the mail connection. The saved employee record remains saved; the page shows a warning about sending. If there is no caught error, it shows the success message instead.

**How did we know what happened?**

A failure-injection or temporarily misconfigured SMTP test would confirm the warning path; no such completed test is recorded here.

**Where to go next:** Previous: [Step 192 — Update Edit Employee](18-shared-base-template-and-page-styling.md#step-192) · Next: [Step 194 — Resend Email Error Handling](18-shared-base-template-and-page-styling.md#step-194)


---

<a id="step-194"></a>

### Step 194: Resend Email Error Handling

**What was happening before this step?**

The “resend setup email” button could face the same Gmail failure, even though the employee account already existed.

**What did we do, and what does it mean?**

We added similar error handling to the resend view. If Gmail is unavailable, the administrator sees a useful message and can try again later; the account record remains intact.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Find these lines inside resendsetupemail().*

```python
    else:
        send_welcome_email(request, employee)
        messages.success(request, 'Password setup email generated successfully.')
```

*2. Replace them with.*

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

*3. Save views.py, then run.*

```powershell
python manage.py check
```

**Understanding the code, one piece at a time**

The resend view does not create another employee. It only asks for another invitation email to be sent. If the mail server fails, the view reports that failure and returns to a usable page rather than showing a crash.

**How did we know what happened?**

Our notes includes follow-up checks and analysis, but not a verified end-to-end failure test for every Gmail sending error.

**Where to go next:** Previous: [Step 193 — Email Error Handling](18-shared-base-template-and-page-styling.md#step-193)

---

### The point of this chapter

The shared `base.html` template is a page frame reused everywhere. Each child page fills in only its own content. The last two steps are about making the app explain email failures accurately, not about changing the shared layout.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

