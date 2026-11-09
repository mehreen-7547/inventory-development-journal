# MODULE 1 — Employee accounts

## Chapter 14: Edit employee (Steps 145–156)

### Before we begin

Administrators could add people, but correcting one employee's details was still awkward.

### What we worked on

We created an edit form and view, linked each employee to their edit page and improved how people select a row on the employee list.

### Picture it this way

Editing is not creating a second employee. The view finds the existing account by its ID and saves new values to that same record. Later steps in this chapter fix the page's selection controls and move styling into CSS.

### Find a step

- [Step 145: Edit Employee Form](#step-145)
- [Step 146: Edit Employee View](#step-146)

---

<a id="step-145"></a>

### Step 145: Edit Employee Form

#### Before this step

An administrator sometimes needs to correct an employee's details without creating a new account. The preceding step, Step 144, covered refine the create employee link, so this was the next part of the development sequence.

#### What we did and why

We defined a separate edit form with the fields that may be changed. It works with an existing user record and does not ask for a new login password.

#### Code and commands

**Step 145, block 1.** This code sample or terminal command was used while working on edit employee form. The content below remains in its original sequence for Step 145, block 1.

```python
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
```

#### How the code works

**Block 1 (python, 18 lines).** In Step 145, edit employee form involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `class EmployeeEditForm(forms.ModelForm):` declares a Python class, grouping related data or behaviour together. `class Meta:` declares a Python class, grouping related data or behaviour together. `model = CustomUser` sets the named value used by the next operation in this file. `fields = [` sets the named value used by the next operation in this file.

**Lines 8–14:** `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Lines 15–18:** `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 145. For Step 145, the proposed way to check edit employee form was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 146 before a complete result could be claimed.

#### Next

[Step 144: Refine the Create Employee link](../Module-1/13-create-employee.md#step-144) · [Step 146: Edit Employee View](#step-146)


---

<a id="step-146"></a>

### Step 146: Edit Employee View

#### Before this step

The edit form existed. The next question was how the page would find the right employee and save changes to that same account. Immediately before this, Step 145 dealt with edit employee form; the work here continued the same part of the application.

#### What we did and why

The employee ID comes from the URL. The view finds that record, fills the form with its current values and, on submission, saves changes back to it. If the ID is invalid, it returns a “not found” response instead of editing someone else.

#### Code and commands

**Step 146, block 1.** This code sample or terminal command was used while working on edit employee view. The content below remains in its original sequence for Step 146, block 1.

```python
from django.shortcuts import get_object_or_404
from .forms import EmployeeEditForm
```

**Step 146, block 2.** This code sample or terminal command was used while working on edit employee view. The content below remains in its original sequence for Step 146, block 2.

```python
@role_required('admin')
def edit_employee(request, employee_id):

    employee = get_object_or_404(
        CustomUser,
        id=employee_id,
        role__in=['manager', 'sales']
    )

    if request.method == 'POST':
        form = EmployeeEditForm(
            request.POST,
            instance=employee
        )

        if form.is_valid():
            form.save()

            messages.success(
                request,
                'Employee updated successfully.'
            )

            return redirect('employee_list')

    else:
        form = EmployeeEditForm(instance=employee)

    return render(
        request,
        'accounts/edit_employee.html',
        {
            'form': form,
            'employee': employee
        }
    )
```

#### How the code works

**Block 1 (python, 2 lines).** In Step 146, edit employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from django.shortcuts import get_object_or_404` makes a Python function, class or module available to the code that follows. `from .forms import EmployeeEditForm` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 36 lines).** In Step 146, edit employee view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@role_required('admin')` adds a check or behaviour around the view before Django runs the view itself. `def edit_employee(request, employee_id):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `employee = get_object_or_404(` sets the named value used by the next operation in this file. `id=employee_id,` sets the named value used by the next operation in this file.

**Lines 8–14:** `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied. `form = EmployeeEditForm(` sets the named value used by the next operation in this file. `instance=employee` sets the named value used by the next operation in this file.

**Lines 15–21:** `if form.is_valid():` is a condition: the code inside this branch only runs when its condition is satisfied. `form.save()` writes the validated form values into the corresponding database record. `messages.success(` prepares feedback that can appear when the user reaches the next page.

**Lines 22–28:** `return redirect('employee_list')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. The `else` branch handles the alternative case when the preceding condition was not satisfied. `form = EmployeeEditForm(instance=employee)` sets the named value used by the next operation in this file.

**Lines 29–35:** `return render(` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Lines 36–36:** The lines starting with `)` supply the values or page content for this part of accounts/views.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 146. For Step 146, the proposed way to check edit employee view was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 147 before a complete result could be claimed.

#### Next

[Step 145: Edit Employee Form](#step-145) · [Step 147: Edit Employee Template](#step-147)
