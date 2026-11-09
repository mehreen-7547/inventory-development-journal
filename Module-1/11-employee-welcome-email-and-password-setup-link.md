# MODULE 1 — Employee accounts

## Chapter 11: Employee welcome email and password-setup link (Steps 128–135)

### Before we begin

An administrator could create employee accounts in Django Admin, but employees should not receive a password chosen by someone else.

### What we worked on

We prepared the employee form, welcome email and secure first-time password link. We also considered link expiry and the ability to resend an invitation.

### Picture it this way

Creating an employee and letting the employee choose a password are separate moments. The account can exist before that first password is set. The welcome message connects those moments.

### Find a step

- [Step 128: Employee Creation Form](#step-128)

---

<a id="step-128"></a>

### Step 128: Employee Creation Form

#### Before this step

Administrators needed to create employee accounts, but employees should choose their own passwords. The preceding step, Step 127, covered email testing, so this was the next part of the development sequence.

#### What we did and why

The creation form records details such as name, email and role while leaving the new account without a usable password. A separate email link will later let that employee choose a private password.

#### Code and commands

**Step 128, block 1.** This code sample or terminal command was used while working on employee creation form. The content below remains in its original sequence for Step 128, block 1.

```python
from django import forms
from .models import CustomUser


class EmployeeCreationForm(forms.ModelForm):

    class Meta:
        model = CustomUser
        fields = ['username', 'first_name', 'last_name', 'email', 'role']

    def save(self, commit=True):
        user = super().save(commit=False)
        user.set_unusable_password()

        if commit:
            user.save()

        return user
```

#### How the code works

**Block 1 (python, 18 lines).** In Step 128, employee creation form involved `admin.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django import forms` makes a Python function, class or module available to the code that follows. `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `class EmployeeCreationForm(forms.ModelForm):` declares a Python class, grouping related data or behaviour together. `class Meta:` declares a Python class, grouping related data or behaviour together.

**Lines 8–14:** `model = CustomUser` sets the named value used by the next operation in this file. `fields = ['username', 'first_name', 'last_name', 'email', 'role']` sets the named value used by the next operation in this file. `def save(self, commit=True):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `user = super().save(commit=False)` sets the named value used by the next operation in this file.

**Lines 15–18:** `if commit:` is a condition: the code inside this branch only runs when its condition is satisfied. `return user` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 128. For Step 128, the proposed way to check employee creation form was to revisit `admin.py` and run the corresponding command or feature to see its result. Development continued in Step 129 before a complete result could be claimed.

#### Next

[Step 127: Email Testing](../Module-1/10-forgot-and-reset-password.md#step-127) · [Step 129: Connect Employee Form](#step-129)
