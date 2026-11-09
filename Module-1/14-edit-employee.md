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
- [Step 147: Edit Employee Template](#step-147)
- [Step 148: Edit Employee URL](#step-148)
- [Step 149: Employee Edit Link](#step-149)

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


---

<a id="step-147"></a>

### Step 147: Edit Employee Template

#### Before this step

The edit view needed an actual page with fields and a save button. Immediately before this, Step 146 dealt with edit employee view; the work here continued the same part of the application.

#### What we did and why

The edit template shows the existing values and any validation errors. The browser sees the form; the view remains responsible for reading and saving the data.

#### Code and commands

**Step 147, block 1.** This code sample or terminal command was used while working on edit employee template. The content below remains in its original sequence for Step 147, block 1.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Edit Employee</title>
</head>
<body>

    <h2>Edit Employee</h2>

    <p>Username: {{ employee.username }}</p>

    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit">
            Save Changes
        </button>
    </form>

    <p>
        <a href="{% url 'employee_list' %}">
            Back to Manage Employees
        </a>
    </p>

</body>
</html>
```

#### How the code works

**Block 1 (html, 29 lines).** In Step 147, edit employee template involved `templates/accounts/edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Edit Employee</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Edit Employee</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Username: {{ employee.username }}</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `<button type="submit">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `<p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'employee_list' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 29–29:** The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 147. For Step 147, the proposed way to check edit employee template was to open the related page in the browser and check that the route reached the intended view or template in `templates/accounts/edit_employee.html`. Development continued in Step 148 before a complete result could be claimed.

#### Next

[Step 146: Edit Employee View](#step-146) · [Step 148: Edit Employee URL](#step-148)


---

<a id="step-148"></a>

### Step 148: Edit Employee URL

#### Before this step

The browser needed a unique address for editing each employee. Immediately before this, Step 147 dealt with edit employee template; the work here continued the same part of the application.

#### What we did and why

The edit URL contains an employee ID. It tells the same edit view which record to load, so we do not need a different Python function for every person.

#### Code and commands

**Step 148, block 1.** This code sample or terminal command was used while working on edit employee url. The content below remains in its original sequence for Step 148, block 1.

```python
path(
    'employees/edit/<int:employee_id>/',
    views.edit_employee,
    name='edit_employee'
),
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 148, edit employee url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `path(` begins a URL rule connecting a browser address with the function that answers it. `name='edit_employee'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 148. For Step 148, the proposed way to check edit employee url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Development continued in Step 149 before a complete result could be claimed.

#### Next

[Step 147: Edit Employee Template](#step-147) · [Step 149: Employee Edit Link](#step-149)


---

<a id="step-149"></a>

### Step 149: Employee Edit Link

#### Before this step

The list showed employees but did not yet let an administrator open one person's edit page. Immediately before this, Step 148 dealt with edit employee url; the work here continued the same part of the application.

#### What we did and why

We added edit links using each row's employee ID. Clicking a link opens the edit form for that particular record.

#### Code and commands

**Step 149, block 1.** This code sample or terminal command was used while working on employee edit link. The content below remains in its original sequence for Step 149, block 1.

```html
<th>Actions</th>
```

**Step 149, block 2.** This code sample or terminal command was used while working on employee edit link. The content below remains in its original sequence for Step 149, block 2.

```html
<th>Status</th>
```

**Step 149, block 3.** This code sample or terminal command was used while working on employee edit link. The content below remains in its original sequence for Step 149, block 3.

```html
<td>
    <a href="{% url 'edit_employee' employee.id %}">
        Edit
    </a>
</td>
```

**Step 149, block 4.** This code sample or terminal command was used while working on employee edit link. The content below remains in its original sequence for Step 149, block 4.

```html
<td colspan="5">No employees found.</td>
```

#### How the code works

**Block 1 (html, 1 lines).** In Step 149, employee edit link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<th>Actions</th>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 1 lines).** In Step 149, employee edit link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<th>Status</th>` creates or closes part of the page structure seen by the browser.

**Block 3 (html, 5 lines).** In Step 149, employee edit link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** The HTML element `<td>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'edit_employee' employee.id %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</td>` creates or closes part of the page structure seen by the browser.

**Block 4 (html, 1 lines).** In Step 149, employee edit link involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<td colspan="5">No employees found.</td>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 149. For Step 149, the proposed way to check employee edit link was to revisit `templates/accounts/employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 150 before a complete result could be claimed.

#### Next

[Step 148: Edit Employee URL](#step-148) · [Step 150: Employee Edit Mode](#step-150)
