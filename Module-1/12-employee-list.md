# MODULE 1 — Employee accounts

## Chapter 12: Employee list (Steps 136–138)

### Before we begin

Employee accounts existed, but an administrator needed one place inside the website to see them together.

### What we worked on

We created a view that reads employee records, an HTML table that displays them and a URL that opens the list.

### Picture it this way

Think of the employee list as the starting screen for account management. At this stage it mostly shows information; the next chapters add ways to create and edit accounts.

### Find a step

- [Step 136: Employee Management](#step-136)

---

<a id="step-136"></a>

### Step 136: Employee Management

#### Before this step

We could create employee accounts, but administrators still needed a screen where they could see them together. The preceding step, Step 135, covered resend setup link, so this was the next part of the development sequence.

#### What we did and why

The employee-list view asks the database for staff accounts and passes the results to an HTML template. A list is the starting point for later create, edit and status actions.

#### Code and commands

**Step 136, block 1.** This code sample or terminal command was used while working on employee management. The content below remains in its original sequence for Step 136, block 1.

```python
from .models import CustomUser
from .decorators import role_required
```

**Step 136, block 2.** This code sample or terminal command was used while working on employee management. The content below remains in its original sequence for Step 136, block 2.

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

#### How the code works

**Block 1 (python, 2 lines).** In Step 136, employee management involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `from .decorators import role_required` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 11 lines).** In Step 136, employee management involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `def employee_list(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `employees = CustomUser.objects.filter(` sets the named value used by the next operation in this file. `role__in=['manager', 'sales']` sets the named value used by the next operation in this file.

**Lines 8–11:** The lines starting with `request,` supply the values or page content for this part of accounts/views.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 136. For Step 136, the proposed way to check employee management was to revisit `accounts/views.py` and run the corresponding command or feature to see its result. Development continued in Step 137 before a complete result could be claimed.

#### Next

[Step 135: Resend Setup Link](../Module-1/11-employee-welcome-email-and-password-setup-link.md#step-135) · [Step 137: Employee List Template](#step-137)
