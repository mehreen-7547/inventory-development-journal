# MODULE 1 — Accounts

## Chapter 15: Activate, deactivate and resend setup email (Steps 157–164)

**Where we are.** Removing an employee entirely can damage history, while an inactive account should be unable to sign in. Lost setup links also need recovery.

**What this chapter gives us.** Use is_active for reversible status changes and add a secure resend action.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain deactivate versus delete and identify the permission checks on admin actions.

### Find a step

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

**Why we are doing this.** Deleting employees would erase useful history, so we needed a reversible way to stop a staff member signing in.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this import.*

```python
from django.views.decorators.http import require_POST
```

*2. Recorded snippet; Then add this function at the bottom.*

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

**How it works.** The status view toggles `is_active` for a selected employee. A deactivated account remains in the database, preserving references while Django authentication rejects it.

**Follow the flow.** `get_object_or_404` selects a Manager or Sales employee by ID. `@require_POST` ensures the action is not triggered merely by opening a link. `employee.is_active = not employee.is_active` flips the account's current state, and `save(update_fields=['is_active'])` writes only that field. Django authentication normally refuses inactive users. The employee record itself remains in the database.

**How we checked it.** The URL and management button are added in Steps 158–159.

**Connects to.** Previous: [Step 156 — Correct employee row selection](14-edit-employee.md#step-156) · Next: [Step 158 — Employee Status URL](15-activate-deactivate-and-resend-setup-email.md#step-158)


---

<a id="step-158"></a>

### Step 158: Employee Status URL

**Why we are doing this.** The activation/deactivation view needs a route carrying the target employee's ID.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside the existing urlpatterns list.*

```python
path(
    'employees/status/<int:employee_id>/',
    views.toggle_employee_status,
    name='toggle_employee_status'
),
```

**How it works.** The named URL connects the action endpoint to its view. Because it changes an account, it should be invoked through a protected request rather than an unsafe public link.

**How we checked it.** The next step adds the actual control on the employee page.

**Connects to.** Previous: [Step 157 — Employee Deactivation](15-activate-deactivate-and-resend-setup-email.md#step-157) · Next: [Step 159 — Deactivate/Reactivate Button](15-activate-deactivate-and-resend-setup-email.md#step-159)


---

<a id="step-159"></a>

### Step 159: Deactivate/Reactivate Button

**Why we are doing this.** Administrators should see each employee's current account state and be able to change it when permitted.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find the existing Save Changes form's closing tag.*

```html
</form>
```

*2. Recorded snippet; Immediately below it, add.*

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

**How it works.** The template renders Activate or Deactivate depending on `is_active`, and submits the action for that particular employee.

The token helps Django reject forged state-changing form submissions.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** Verify with a non-admin and an admin, and check that a deactivated account cannot log in.

**Connects to.** Previous: [Step 158 — Employee Status URL](15-activate-deactivate-and-resend-setup-email.md#step-158) · Next: [Step 160 — Resend Setup Email](15-activate-deactivate-and-resend-setup-email.md#step-160)


---

<a id="step-160"></a>

### Step 160: Resend Setup Email

**Why we are doing this.** An active employee who never received the welcome message may need a fresh password-setup email.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this function at the bottom.*

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

**How it works.** The resend view obtains the selected account and calls the existing welcome-email helper, rather than duplicating token/email logic.

**Follow the flow.** The resend endpoint is admin-only and POST-only. It first checks whether the employee is inactive or already has a usable password. In either case it reports why a setup email is unnecessary or disallowed. Only an eligible account reaches `send_welcome_email`, which reuses the secure link mechanism instead of duplicating token logic.

**How we checked it.** The URL and button are wired in Steps 161–162.

**Connects to.** Previous: [Step 159 — Deactivate/Reactivate Button](15-activate-deactivate-and-resend-setup-email.md#step-159) · Next: [Step 161 — Resend Setup Email URL](15-activate-deactivate-and-resend-setup-email.md#step-161)


---

<a id="step-161"></a>

### Step 161: Resend Setup Email URL

**Why we are doing this.** The resend helper needs a named route so the employee list can address a specific employee.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this inside the existing urlpatterns list.*

```python
path(
    'employees/resend-setup/<int:employee_id>/',
    views.resend_setup_email,
    name='resend_setup_email'
),
```

**How it works.** A URL parameter identifies the account; the view applies role permissions and performs the sending action.

**How we checked it.** The corresponding button is added next.

**Connects to.** Previous: [Step 160 — Resend Setup Email](15-activate-deactivate-and-resend-setup-email.md#step-160) · Next: [Step 162 — Resend Setup Email Button](15-activate-deactivate-and-resend-setup-email.md#step-162)


---

<a id="step-162"></a>

### Step 162: Resend Setup Email Button

**Why we are doing this.** The administrator needs a visible control for resending a setup link from an employee's row.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Immediately below its closing </form> tag, add.*

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

**How it works.** The template submits a protected request to the named resend route. This calls the same email mechanism used during onboarding.

The token helps Django reject forged state-changing form submissions.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** With the console backend, verify the terminal shows the outgoing message; real mail is configured later.

**Connects to.** Previous: [Step 161 — Resend Setup Email URL](15-activate-deactivate-and-resend-setup-email.md#step-161) · Next: [Step 163 — Home Page Navigation](15-activate-deactivate-and-resend-setup-email.md#step-163)


---

<a id="step-163"></a>

### Step 163: Home Page Navigation

**Why we are doing this.** The new employee-management functions were hard to discover without a central navigation link.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find.*

```html
<p>Administrator Dashboard</p>
```

*2. Recorded snippet; Immediately below it, add.*

```html
{% if user.role == 'admin' %}

    <p>
        <a href="{% url 'employee_list' %}">
            Manage Employees
        </a>
    </p>

{% endif %}
```

**How it works.** The homepage gains a link for authorized staff to reach the employee list. The visibility should match the server-side permission rule.

**How we checked it.** A permitted user should reach the list; an unauthorized user must remain blocked even by direct URL.

**Connects to.** Previous: [Step 162 — Resend Setup Email Button](15-activate-deactivate-and-resend-setup-email.md#step-162) · Next: [Step 164 — Home Template Review](15-activate-deactivate-and-resend-setup-email.md#step-164)


---

<a id="step-164"></a>

### Step 164: Home Template Review

**Why we are doing this.** We reviewed the homepage template so the navigation, role labels, and messages remained coherent as features were added.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Replace your entire Dashboard section with.*

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

**How it works.** The code shows the current home structure and where employee-management links belong. This is a consolidation/review step, not a new dashboard feature.

**How we checked it.** No independent feature test was recorded in this review.

**Connects to.** Previous: [Step 163 — Home Page Navigation](15-activate-deactivate-and-resend-setup-email.md#step-163) · Next: [Step 165 — Employee Management Messages](16-message-styling.md#step-165)

---

**Chapter recap.** Use is_active for reversible status changes and add a secure resend action.

**Practice explaining:** Explain deactivate versus delete and identify the permission checks on admin actions.

