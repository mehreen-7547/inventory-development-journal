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
