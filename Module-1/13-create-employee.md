# MODULE 1 — Employee accounts

## Chapter 13: Create employee (Steps 139–144)

### Before we begin

We could see employees in a list, but adding a new person still relied on Django's built-in administration page.

### What we worked on

We built our own Create Employee page with a form, a view that saves the account, role restrictions and a way to open the form from the list.

### Picture it this way

When an admin first opens the page, Django shows empty fields. After the admin submits them, Django validates the values, saves the employee and starts the welcome-email process. The view, template and URL work together to make the journey possible.

### Find a step

- [Step 139: Create Employee View](#step-139)

---

<a id="step-139"></a>

### Step 139: Create Employee View

#### Before this step

We now had a page listing employees. An administrator needed a way to add another person from inside the application. The preceding step, Step 138, covered employee list url, so this was the next part of the development sequence.

#### What we did and why

The create view shows an empty form when opened and checks filled-in values when submitted. If they are valid, it saves the employee, tries to send the welcome message and returns to the list. The URL and HTML page are connected in following steps.

#### Code and commands

**Step 139, block 1.** This code sample or terminal command was used while working on create employee view. The content below remains in its original sequence for Step 139, block 1.

```python
from .forms import EmployeeCreationForm
from .utils import send_welcome_email
```

**Step 139, block 2.** This code sample or terminal command was used while working on create employee view. The content below remains in its original sequence for Step 139, block 2.

```python
@role_required('admin')
def create_employee(request):

    if request.method == 'POST':
        form = EmployeeCreationForm(request.POST)

        if form.is_valid():
            employee = form.save()

            send_welcome_email(request, employee)

            messages.success(
                request,
                'Employee created successfully.'
            )

            return redirect('employee_list')

    else:
        form = EmployeeCreationForm()

    return render(
        request,
        'accounts/create_employee.html',
        {'form': form}
    )
```

#### How the code works

**Block 1 (python, 2 lines).** In Step 139, create employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from .forms import EmployeeCreationForm` makes a Python function, class or module available to the code that follows. `from .utils import send_welcome_email` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 26 lines).** In Step 139, create employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `def create_employee(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied. `form = EmployeeCreationForm(request.POST)` sets the named value used by the next operation in this file.

**Lines 8–14:** `form.save()` writes the validated form values into the corresponding database record. `messages.success(` prepares feedback that can appear when the user reaches the next page.

**Lines 15–21:** `return redirect('employee_list')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. The `else` branch handles the alternative case when the preceding condition was not satisfied. `form = EmployeeCreationForm()` sets the named value used by the next operation in this file.

**Lines 22–26:** `return render(` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 139. For Step 139, the proposed way to check create employee view was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 140 before a complete result could be claimed.

#### Next

[Step 138: Employee List URL](../Module-1/12-employee-list.md#step-138) · [Step 140: Restrict Employee Roles](#step-140)
