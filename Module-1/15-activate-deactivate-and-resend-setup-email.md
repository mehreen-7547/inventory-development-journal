# MODULE 1 — Employee accounts

## Chapter 15: Activate, deactivate and resend setup email (Steps 157–164)

### Before we begin

Sometimes an employee leaves or needs another password-setup email. Deleting the employee is not a good answer because it removes useful history.

### What we worked on

We added an active/inactive switch, a button to request another setup email and links to reach these actions.

### Picture it this way

Deactivation leaves the account record in the database but prevents normal sign-in. Resending an email reuses the existing employee, after checking whether another setup message is appropriate.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 157: Employee Deactivation](#step-157)
- [Step 158: Employee Status URL](#step-158)
- [Step 159: Deactivate/Reactivate Button](#step-159)
- [Step 160: Resend Setup Email](#step-160)
- [Step 161: Resend Setup Email URL](#step-161)
- [Step 162: Resend Setup Email Button](#step-162)
- [Step 163: Home Page Navigation](#step-163)
- [Step 164: Home Template Review](#step-164)

---

<a id="step-157"></a>

### Step 157: Employee Deactivation

**What was happening before this step?**

We needed to stop a former employee from signing in without deleting their account and losing the record of their past actions.

**What did we do, and what does it mean?**

The `is_active` field is a yes/no value on the user record. Changing it to false disables normal login while leaving the employee row in the database. Changing it back can reactivate the same account.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add this import.*

```python
from django.views.decorators.http import require_POST
```

*2. Then add this function at the bottom.*

```python
@role_required('admin')
@require_POST
def toggle_employee_status(request, employee_id):

    employee = get_object_or_404(
        CustomUser,
        id=employee_id,
        role__in=['manager', 'sales']
    )

    employee.is_active = not employee.is_active
    employee.save(update_fields=['is_active'])

    if employee.is_active:
        messages.success(
            request,
            'Employee activated successfully.'
        )
    else:
        messages.success(
            request,
            'Employee deactivated successfully.'
        )

    return redirect('employee_list')
```

**Understanding the code, one piece at a time**

`employee.is_active = not employee.is_active` changes true to false or false to true. Saving that field changes whether the account can normally log in. It does not remove the person or the record from the database.

**How did we know what happened?**

The URL and management button are added in Steps 158–159.

**Where to go next:** Previous: [Step 156 — Correct employee row selection](14-edit-employee.md#step-156) · Next: [Step 158 — Employee Status URL](15-activate-deactivate-and-resend-setup-email.md#step-158)


---

<a id="step-158"></a>

### Step 158: Employee Status URL

**What was happening before this step?**

The activate/deactivate function needed an address that specified whose account should change.

**What did we do, and what does it mean?**

The URL carries the employee ID to the protected view. The view performs the database change; the URL only helps Django find it.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside the existing urlpatterns list.*

```python
path(
    'employees/status/<int:employee_id>/',
    views.toggle_employee_status,
    name='toggle_employee_status'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The next step adds the actual control on the employee page.

**Where to go next:** Previous: [Step 157 — Employee Deactivation](15-activate-deactivate-and-resend-setup-email.md#step-157) · Next: [Step 159 — Deactivate/Reactivate Button](15-activate-deactivate-and-resend-setup-email.md#step-159)


---

<a id="step-159"></a>

### Step 159: Deactivate/Reactivate Button

**What was happening before this step?**

The administrator needed a visible way to change an employee's account state.

**What did we do, and what does it mean?**

A button shows the appropriate action, such as Deactivate or Reactivate, based on the current status. The request is sent as an action, not a harmless page-opening link.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Find the existing Save Changes form's closing tag.*

```html
</form>
```

*2. Immediately below it, add.*

```html
<form method="POST"
      action="{% url 'toggle_employee_status' employee.id %}">

    {% csrf_token %}

    {% if employee.is_active %}

        <button type="submit">
            Deactivate Employee
        </button>

    {% else %}

        <button type="submit">
            Reactivate Employee
        </button>

    {% endif %}

</form>
```

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Verify with a non-admin and an admin, and check that a deactivated account cannot log in.

**Where to go next:** Previous: [Step 158 — Employee Status URL](15-activate-deactivate-and-resend-setup-email.md#step-158) · Next: [Step 160 — Resend Setup Email](15-activate-deactivate-and-resend-setup-email.md#step-160)


---

<a id="step-160"></a>

### Step 160: Resend Setup Email

**What was happening before this step?**

Some existing employees might still have no usable password because their first setup email was lost.

**What did we do, and what does it mean?**

The resend view checks whether sending another setup link makes sense for that employee. It should not send a first-time setup link to every employee without checking their current account state.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add this function at the bottom.*

```python
@role_required('admin')
@require_POST
def resend_setup_email(request, employee_id):

    employee = get_object_or_404(
        CustomUser,
        id=employee_id,
        role__in=['manager', 'sales']
    )

    if not employee.is_active:
        messages.error(
            request,
            'Cannot send setup email to an inactive employee.'
        )

    elif employee.has_usable_password():
        messages.error(
            request,
            'Employee has already set a password.'
        )

    else:
        send_welcome_email(request, employee)

        messages.success(
            request,
            'Password setup email generated successfully.'
        )

    return redirect('employee_list')
```

**Understanding the code, one piece at a time**

The resend view starts by checking the chosen account. If the user is inactive or already has a usable password, the code may refuse sending another first-time setup link. If eligible, it calls the email helper created earlier.

**How did we know what happened?**

The URL and button are wired in Steps 161–162.

**Where to go next:** Previous: [Step 159 — Deactivate/Reactivate Button](15-activate-deactivate-and-resend-setup-email.md#step-159) · Next: [Step 161 — Resend Setup Email URL](15-activate-deactivate-and-resend-setup-email.md#step-161)


---

<a id="step-161"></a>

### Step 161: Resend Setup Email URL

**What was happening before this step?**

The resend code existed, but each employee's button needed an address that reached it.

**What did we do, and what does it mean?**

The URL includes that employee's ID and points to the resend view. The same helper can then build a fresh link for the correct person.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add this inside the existing urlpatterns list.*

```python
path(
    'employees/resend-setup/<int:employee_id>/',
    views.resend_setup_email,
    name='resend_setup_email'
),
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The corresponding button is added next.

**Where to go next:** Previous: [Step 160 — Resend Setup Email](15-activate-deactivate-and-resend-setup-email.md#step-160) · Next: [Step 162 — Resend Setup Email Button](15-activate-deactivate-and-resend-setup-email.md#step-162)


---

<a id="step-162"></a>

### Step 162: Resend Setup Email Button

**What was happening before this step?**

An administrator should be able to request the fresh link from the employee-management screen rather than using Django Admin or the terminal.

**What did we do, and what does it mean?**

The page gained a resend button for eligible employee accounts. The button starts a server-side action; it does not contain the employee's password or the actual reset token.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Immediately below its closing </form> tag, add.*

```html
{% if employee.is_active and not employee.has_usable_password %}

    <form method="POST"
          action="{% url 'resend_setup_email' employee.id %}">

        {% csrf_token %}

        <button type="submit">
            Resend Setup Email
        </button>

    </form>

{% endif %}
```

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

With the console backend, verify the terminal shows the outgoing message; real mail is configured later.

**Where to go next:** Previous: [Step 161 — Resend Setup Email URL](15-activate-deactivate-and-resend-setup-email.md#step-161) · Next: [Step 163 — Home Page Navigation](15-activate-deactivate-and-resend-setup-email.md#step-163)


---

<a id="step-163"></a>

### Step 163: Home Page Navigation

**What was happening before this step?**

With employee management added, users needed a way to find it from the homepage.

**What did we do, and what does it mean?**

We added navigation pointing to the relevant screen for people with permission. This reduces the need to remember URLs manually.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Find.*

```html
<p>Administrator Dashboard</p>
```

*2. Immediately below it, add.*

```html
{% if user.role == 'admin' %}

    <p>
        <a href="{% url 'employee_list' %}">
            Manage Employees
        </a>
    </p>

{% endif %}
```

**How did we know what happened?**

A permitted user should reach the list; an unauthorized user must remain blocked even by direct URL.

**Where to go next:** Previous: [Step 162 — Resend Setup Email Button](15-activate-deactivate-and-resend-setup-email.md#step-162) · Next: [Step 164 — Home Template Review](15-activate-deactivate-and-resend-setup-email.md#step-164)


---

<a id="step-164"></a>

### Step 164: Home Template Review

**What was happening before this step?**

After adding links and role-specific content, we reviewed the homepage as a whole.

**What did we do, and what does it mean?**

The check was about a coherent experience: correct links for each role, readable account details and useful notices. It was a review rather than an entirely new Django feature.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Replace your entire Dashboard section with.*

```html
<!-- DASHBOARD -->
{% if request.user.role == 'admin' %}

    <p>Administrator Dashboard</p>

    <p>
        <a href="{% url 'employee_list' %}">
            Manage Employees
        </a>
    </p>

{% elif request.user.role == 'manager' %}

    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}

    <p>Sales Staff Dashboard</p>

{% endif %}
```

**How did we know what happened?**

No independent feature test was recorded in this review.

**Where to go next:** Previous: [Step 163 — Home Page Navigation](15-activate-deactivate-and-resend-setup-email.md#step-163) · Next: [Step 165 — Employee Management Messages](16-message-styling.md#step-165)

---

### The point of this chapter

Deactivation leaves the account record in the database but prevents normal sign-in. Resending an email reuses the existing employee, after checking whether another setup message is appropriate.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

