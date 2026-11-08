# Module 1, Chapter 18: Shared base template and page styling

## Step 179: Dashboard Template Inheritance

Your existing `home.html` is correct functionally. We only need to connect it to `base.html` and remove the duplicated navigation and messages.

#### Step 1 — Replace `home.html`

**File:** `core/templates/core/home.html`

Replace the entire contents with:

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

#### Step 2 — Understand the changes

| Before | Now |
|---|---|
| Repeated HTML structure | Inherited from `base.html` |
| Separate logout form | Shared navbar handles logout |
| Separate Change Password link | Shared navbar handles it |
| Separate messages section | Shared layout displays messages |
| Role-specific dashboard | Preserved |
| Django Admin link | Preserved for staff users |

#### Step 3 — Test

Run the Django server if it isn't already running:

```powershell
python manage.py runserver
```

Open `http://127.0.0.1:8000/`.

Verify that the dashboard displays the Bootstrap navbar, welcome message, and correct role-specific heading. Check that **Manage Employees**, **Change Password**, and **Logout** appear only once.

*Previous: Step 178 (Change base.html)  |  Next: Step 180 (Update Login Page)*

---

## Step 180: Update Login Page

Your existing login form is correct. We'll connect it to `base.html`, apply basic Bootstrap styling, and add a link to the password-reset feature we've already implemented.

#### Step 1 — Replace `login.html`

**File:** `templates/accounts/login.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- The page now inherits the shared Bootstrap layout and navbar.
- The login form is centred with basic Bootstrap styling.
- Messages are displayed by `base.html`, so we don't repeat them here.
- The **Forgot Password?** link opens our existing password-reset page.
- Username, password, and CSRF protection remain unchanged.

#### Step 3 — Test

Open `http://127.0.0.1:8000/login/` after logging out.

Verify that the form is centred, the Login button works, the Forgot Password link opens correctly, and an incorrect password displays an error message without duplicating it.

*Previous: Step 179 (Dashboard Template Inheritance)  |  Next: Step 181 (Update Change Password Page)*

---

## Step 181: Update Change Password Page

#### Step 1 — Replace `change_password.html`

**File:** `templates/accounts/change_password.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and buttons.
- Added a **Cancel** link to the dashboard.
- Preserved `{{ form.as_p }}`, including Django's password validation and field errors.
- Removed the duplicated HTML structure.

#### Step 3 — Test

1. Log in and select **Change Password**.
2. Enter an incorrect current password.
3. Submit the form.
4. Verify that Django displays a validation error and does not change the password.
5. Click **Cancel** and verify that you return to the dashboard.

This is still the functional Bootstrap interface, not the final visual design.

*Previous: Step 180 (Update Login Page)  |  Next: Step 182 (Update Forgot Password Page)*

---

## Step 182: Update Forgot Password Page

#### Step 1 — Replace the existing HTML

**File:** The password-reset form template you just opened.

Replace its entire contents with:

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

#### Step 2 — What changed?

- Uses the shared `base.html` layout.
- Centres the form using Bootstrap.
- Styles the submit button.
- Preserves Django's email field, validation, and CSRF protection.
- Keeps the **Back to Login** link.

#### Step 3 — Test

1. Open the login page.
2. Click **Forgot Password?**
3. Confirm the updated page displays correctly.
4. Click **Back to Login** and verify that it works.
5. Return and submit a registered test email address. Verify that the password-reset confirmation page appears.

*Previous: Step 181 (Update Change Password Page)  |  Next: Step 183 (Update Reset Confirmation)*

---

## Step 183: Update Reset Confirmation

#### Step 1 — Replace `password_reset_done.html`

**File:** `templates/accounts/password_reset_done.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and an information alert.
- Styled the **Back to Login** link.
- Preserved the existing confirmation message.

**Important:** The message intentionally does not reveal whether an email address belongs to an account. This helps prevent account enumeration.

#### Step 3 — Test

1. Open **Forgot Password**.
2. Submit a test email address.
3. Verify that the **Check Your Email** page appears.
4. Click **Back to Login** and confirm that it works.

*Previous: Step 182 (Update Forgot Password Page)  |  Next: Step 184 (Update Set New Password Page)*

---

## Step 184: Update Set New Password Page

#### Step 1 — Replace `password_reset_confirm.html`

**File:** `templates/accounts/password_reset_confirm.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and buttons.
- Preserved the `validlink` condition.
- Preserved Django's password validation and CSRF protection.
- Added a warning alert for invalid or expired links.

#### Step 3 — Test

Use a test employee account:

1. Request a password-reset email.
2. Open the reset link and verify that the **Set New Password** form appears.
3. Set a valid new password and submit.
4. Open the same reset link again. It should now show **Invalid Reset Link**.
5. Click **Request New Link** and verify that the Forgot Password page opens.

*Previous: Step 183 (Update Reset Confirmation)  |  Next: Step 185 (Update Reset Complete Page)*

---

## Step 185: Update Reset Complete Page

#### Step 1 — Replace `password_reset_complete.html`

**File:** `templates/accounts/password_reset_complete.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap styling and a success alert.
- Preserved the confirmation message.
- Preserved the **Go to Login** link.

#### Step 3 — Test

1. Complete a password reset using a test account.
2. Verify that **Password Reset Successful** appears.
3. Click **Go to Login**.
4. Confirm that you can log in using the new password.

This completes the shared-layout updates for the authentication pages.

*Previous: Step 184 (Update Set New Password Page)  |  Next: Step 186 (Update Employee List)*

---

## Step 186: Update Employee List

#### Step 1 — Replace `employee_list.html`

**File:** `templates/accounts/employee_list.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Applied Bootstrap table and button styling.
- Preserved the existing external `style.css`.
- Removed duplicate message handling; `base.html` now handles it.
- Preserved **Add New Employee**, **Edit Employee**, **Cancel Edit**, and clickable employee rows.
- Added visual status indicators for active and inactive employees.

#### Step 3 — Test

  {@body const checks = ["Employee list displays correctly","Add New Employee opens the creation form","Edit Employee enables clickable rows","Clicking an employee opens the correct edit form","Cancel Edit returns to normal list view"]}
  {@body const [done,setDone] = DIL.useState([])}
  {#each checks as check,i}
    setDone(old=>v?[...old,i]:old.filter(x=>x!==i))} lineThrough={false}>{check}
  {/each}

Next, we'll update **Create Employee**, followed by **Edit Employee**.

*Previous: Step 185 (Update Reset Complete Page)  |  Next: Step 187 (Update Create Employee)*

---

## Step 187: Update Create Employee

#### Step 1 — Replace `create_employee.html`

**File:** `templates/accounts/create_employee.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Added Bootstrap layout and buttons.
- Added a **Cancel** button.
- Preserved `{{ form.as_p }}`, form validation, and CSRF protection.
- Kept the existing employee creation and welcome-email workflow unchanged.

#### Step 3 — Test

{@body const checks=["Create Employee page displays correctly","All five fields appear: username, first name, last name, email and role","Submitting an empty form shows validation errors","Cancel returns to Manage Employees"]}
{@body const [done,setDone]=DIL.useState([])}

  {#each checks as check,i}
    setDone(old=>v?[...old,i]:old.filter(x=>x!==i))} lineThrough={false}>{check}
  {/each}

Next: **Edit Employee**, including the activation/deactivation and resend setup email controls.

*Previous: Step 186 (Update Employee List)  |  Next: Step 188 (Fix Form Styling)*

---

## Step 188: Fix Form Styling

Your screenshot shows that the page layout is working, but the **form fields are still using Django's default appearance**. The labels, inputs, and help text are not aligned consistently.

This happens because `{{ form.as_p }}` doesn't automatically apply Bootstrap styling to the input fields.

#### Step 1 — Update `accounts/forms.py`

Find `EmployeeCreationForm` and its existing `__init__()` method.

**Add this code at the end of that method**, after the role choices:

```python
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

Keep the existing code unchanged.

This applies Bootstrap styling to the five employee fields without adding a new library or changing the form's behaviour. Your instructor's `backend/secondproject/testapp/forms.py` also uses widget attributes to style form fields.

#### Step 2 — Check the result

Save `forms.py` and refresh the Create Employee page.

You should now see full-width input boxes, vertically arranged labels, and a styled role dropdown. This is still the functional UI, not the final design.

*Previous: Step 187 (Update Create Employee)  |  Next: Step 189 (Change accounts/forms.py)*

---

## Step 189: Change accounts/forms.py

In `accounts/forms.py`, find the `__init__()` method inside **EmployeeCreationForm**.

Add the new code immediately after the `self.fields['role'].choices` section.

Your complete `__init__()` method should look like this:

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

**Do not change** the `save()` method or `EmployeeEditForm` yet.

Save the file using `Ctrl + S`, then refresh the Create Employee page.

*Previous: Step 188 (Fix Form Styling)  |  Next: Step 190 (Correct Form Styling)*

---

## Step 190: Correct Form Styling

**Found the problem.** You added the Bootstrap styling inside `EmployeeEditForm`, not `EmployeeCreationForm`.

Keep that code where it is because we'll need it for editing employees too.

#### Fix — `accounts/forms.py`

Find `EmployeeCreationForm` and replace **only its `__init__()` method** with:

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

**Do not change** `save()` or `EmployeeEditForm`.

#### Verify

1. Save `forms.py`.
2. Refresh the Create Employee page.
3. The text fields and role dropdown should now occupy the available form width.

*Previous: Step 189 (Change accounts/forms.py)  |  Next: Step 191 (Change accounts/forms.py)*

---

## Step 191: Change accounts/forms.py

**File:** `accounts/forms.py`

Replace the entire file with this code. Both **Create Employee** and **Edit Employee** will have Bootstrap-styled fields.

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

**After replacing:**

1. Save using `Ctrl + S`.
2. Refresh the Create Employee page.
3. Verify that all input fields and the Role dropdown are full-width.

The existing employee creation, editing, role restrictions, and password-setup functionality remain unchanged.

*Previous: Step 190 (Correct Form Styling)  |  Next: Step 192 (Update Edit Employee)*

---

## Step 192: Update Edit Employee

#### Step 1 — Replace `edit_employee.html`

**File:** `templates/accounts/edit_employee.html`

Replace the entire contents with:

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

#### Step 2 — What changed?

- Connected the page to `base.html`.
- Applied Bootstrap styling to the layout and buttons.
- Added a visual indicator for employee status.
- Preserved the separate POST forms and CSRF protection.
- Preserved the condition for displaying **Resend Setup Email**.
- Kept all existing backend functionality unchanged.

#### Step 3 — Test

{@body const checks=["Employee details display correctly","Save Changes updates employee information","Deactivate Employee works","Reactivate Employee works","Resend Setup Email appears only when eligible","Back to Manage Employees works"]}
{@body const [done,setDone]=DIL.useState([])}

  {#each checks as check,i}
    setDone(old=>v?[...old,i]:old.filter(x=>x!==i))} lineThrough={false}>{check}
  {/each}

Once verified, all three employee management pages will use the shared layout. We'll then continue with **Module 1 error handling and automated tests**.

*Previous: Step 191 (Change accounts/forms.py)  |  Next: Step 193 (Email Error Handling)*

---
