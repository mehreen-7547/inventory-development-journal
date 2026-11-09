# MODULE 1 — Employee accounts

## Chapter 15: Activate, deactivate and resend setup email (Steps 157–164)

### Before we begin

Sometimes an employee leaves or needs another password-setup email. Deleting the employee is not a good answer because it removes useful history.

### What we worked on

We added an active/inactive switch, a button to request another setup email and links to reach these actions.

### Picture it this way

Deactivation leaves the account record in the database but prevents normal sign-in. Resending an email reuses the existing employee, after checking whether another setup message is appropriate.

### Find a step

- [Step 157: Employee Deactivation](#step-157)
- [Step 158: Employee Status URL](#step-158)
- [Step 159: Deactivate/Reactivate Button](#step-159)
- [Step 160: Resend Setup Email](#step-160)
- [Step 161: Resend Setup Email URL](#step-161)
- [Step 162: Resend Setup Email Button](#step-162)

---

<a id="step-157"></a>

### Step 157: Employee Deactivation

#### Before this step

We needed to stop a former employee from signing in without deleting their account and losing their past activity history. The preceding step, Step 156, covered correct employee row selection, so this was the next part of the development sequence.

#### What we did and why

The is_active field is a yes/no value on the user record. Changing it to false disables normal login while leaving the employee row in the database. Changing it back can reactivate the same account.

#### Code and commands

**Step 157, block 1.** This code sample or terminal command was used while working on employee deactivation. The content below remains in its original sequence for Step 157, block 1.

```python
from django.views.decorators.http import require_POST
```

**Step 157, block 2.** This code sample or terminal command was used while working on employee deactivation. The content below remains in its original sequence for Step 157, block 2.

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

#### How the code works

**Block 1 (python, 1 lines).** In Step 157, employee deactivation involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.views.decorators.http import require_POST` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 25 lines).** In Step 157, employee deactivation involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `@require_POST` adds a check or behaviour around the view before Django runs the view itself. `def toggle_employee_status(request, employee_id):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `employee = get_object_or_404(` sets the named value used by the next operation in this file.

**Lines 8–14:** `role__in=['manager', 'sales']` sets the named value used by the next operation in this file. `employee.is_active = not employee.is_active` sets the named value used by the next operation in this file. `employee.save(update_fields=['is_active'])` sets the named value used by the next operation in this file. `if employee.is_active:` is a condition: the code inside this branch only runs when its condition is satisfied.

**Lines 15–21:** `messages.success(` prepares feedback that can appear when the user reaches the next page. The `else` branch handles the alternative case when the preceding condition was not satisfied.

**Lines 22–25:** `return redirect('employee_list')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 157. For Step 157, the proposed way to check employee deactivation was to revisit `accounts/views.py` and run the corresponding command or feature to see its result. Development continued in Step 158 before a complete result could be claimed.

#### Next

[Step 156: Correct employee row selection](../Module-1/14-edit-employee.md#step-156) · [Step 158: Employee Status URL](#step-158)


---

<a id="step-158"></a>

### Step 158: Employee Status URL

#### Before this step

The activate/deactivate function needed an address that specified whose account should change. Immediately before this, Step 157 dealt with employee deactivation; the work here continued the same part of the application.

#### What we did and why

The URL carries the employee ID to the protected view. The view performs the database change; the URL only helps Django find it.

#### Code and commands

**Step 158, block 1.** This code sample or terminal command was used while working on employee status url. The content below remains in its original sequence for Step 158, block 1.

```python
path(
    'employees/status/<int:employee_id>/',
    views.toggle_employee_status,
    name='toggle_employee_status'
),
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 158, employee status url involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `path(` begins a URL rule connecting a browser address with the function that answers it. `name='toggle_employee_status'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 158. For Step 158, the proposed way to check employee status url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 159 before a complete result could be claimed.

#### Next

[Step 157: Employee Deactivation](#step-157) · [Step 159: Deactivate/Reactivate Button](#step-159)


---

<a id="step-159"></a>

### Step 159: Deactivate/Reactivate Button

#### Before this step

The administrator needed a visible way to change an employee's account state. Immediately before this, Step 158 dealt with employee status url; the work here continued the same part of the application.

#### What we did and why

A button shows the appropriate action, such as Deactivate or Reactivate, based on the current status. The request is sent as an action, not a harmless page-opening link.

#### Code and commands

**Step 159, block 1.** This code sample or terminal command was used while working on deactivate/reactivate button. The content below remains in its original sequence for Step 159, block 1.

```html
</form>
```

**Step 159, block 2.** This code sample or terminal command was used while working on deactivate/reactivate button. The content below remains in its original sequence for Step 159, block 2.

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

#### How the code works

**Block 1 (html, 1 lines).** In Step 159, deactivate/reactivate button involved `templates/accounts/edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 20 lines).** In Step 159, deactivate/reactivate button involved `templates/accounts/edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The form sends submitted fields to the server as a POST request, rather than just opening a page. `action="{% url 'toggle_employee_status' employee.id %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. `{% if employee.is_active %}` decides which HTML is included based on the value Django provided.

**Lines 8–14:** The HTML element `<button type="submit">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser.

**Lines 15–20:** The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 159. For Step 159, the proposed way to check deactivate/reactivate button was to revisit `templates/accounts/edit_employee.html` and run the corresponding command or feature to see its result. Development continued in Step 160 before a complete result could be claimed.

#### Next

[Step 158: Employee Status URL](#step-158) · [Step 160: Resend Setup Email](#step-160)


---

<a id="step-160"></a>

### Step 160: Resend Setup Email

#### Before this step

Some existing employees might still have no usable password because their first setup email was lost. Immediately before this, Step 159 dealt with deactivate/reactivate button; the work here continued the same part of the application.

#### What we did and why

The resend view checks whether sending another setup link makes sense for that employee. It should not send a first-time setup link to every employee without checking their current account state.

#### Code and commands

**Step 160, block 1.** This code sample or terminal command was used while working on resend setup email. The content below remains in its original sequence for Step 160, block 1.

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

#### How the code works

**Block 1 (python, 31 lines).** In Step 160, resend setup email involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `@require_POST` adds a check or behaviour around the view before Django runs the view itself. `def resend_setup_email(request, employee_id):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `employee = get_object_or_404(` sets the named value used by the next operation in this file.

**Lines 8–14:** `role__in=['manager', 'sales']` sets the named value used by the next operation in this file. `if not employee.is_active:` is a condition: the code inside this branch only runs when its condition is satisfied. `messages.error(` prepares feedback that can appear when the user reaches the next page.

**Lines 15–21:** `elif employee.has_usable_password():` is a condition: the code inside this branch only runs when its condition is satisfied. `messages.error(` prepares feedback that can appear when the user reaches the next page.

**Lines 22–28:** The `else` branch handles the alternative case when the preceding condition was not satisfied. `messages.success(` prepares feedback that can appear when the user reaches the next page.

**Lines 29–31:** `return redirect('employee_list')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 160. For Step 160, the proposed way to check resend setup email was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 161 before a complete result could be claimed.

#### Next

[Step 159: Deactivate/Reactivate Button](#step-159) · [Step 161: Resend Setup Email URL](#step-161)


---

<a id="step-161"></a>

### Step 161: Resend Setup Email URL

#### Before this step

The resend code existed, but each employee's button needed an address that reached it. Immediately before this, Step 160 dealt with resend setup email; the work here continued the same part of the application.

#### What we did and why

The URL includes that employee's ID and points to the resend view. The same helper can then build a fresh link for the correct person.

#### Code and commands

**Step 161, block 1.** This code sample or terminal command was used while working on resend setup email url. The content below remains in its original sequence for Step 161, block 1.

```python
path(
    'employees/resend-setup/<int:employee_id>/',
    views.resend_setup_email,
    name='resend_setup_email'
),
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 161, resend setup email url involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `path(` begins a URL rule connecting a browser address with the function that answers it. `name='resend_setup_email'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 161. For Step 161, the proposed way to check resend setup email url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 162 before a complete result could be claimed.

#### Next

[Step 160: Resend Setup Email](#step-160) · [Step 162: Resend Setup Email Button](#step-162)


---

<a id="step-162"></a>

### Step 162: Resend Setup Email Button

#### Before this step

An administrator should be able to request the fresh link from the employee-management screen rather than using Django Admin or the terminal. Immediately before this, Step 161 dealt with resend setup email url; the work here continued the same part of the application.

#### What we did and why

The page gained a resend button for eligible employee accounts. The button starts a server-side action; it does not contain the employee's password or the actual reset token.

#### Code and commands

**Step 162, block 1.** This code sample or terminal command was used while working on resend setup email button. The content below remains in its original sequence for Step 162, block 1.

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

#### How the code works

**Block 1 (html, 14 lines).** In Step 162, resend setup email button involved `templates/accounts/edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if employee.is_active and not employee.has_usable_password %}` decides which HTML is included based on the value Django provided. The form sends submitted fields to the server as a POST request, rather than just opening a page. `action="{% url 'resend_setup_email' employee.id %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 8–14:** The HTML element `<button type="submit">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 162. For Step 162, the proposed way to check resend setup email button was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 163 before a complete result could be claimed.

#### Next

[Step 161: Resend Setup Email URL](#step-161) · [Step 163: Home Page Navigation](#step-163)
