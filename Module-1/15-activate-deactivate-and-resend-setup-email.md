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
