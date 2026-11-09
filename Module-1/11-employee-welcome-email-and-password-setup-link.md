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
- [Step 129: Connect Employee Form](#step-129)
- [Step 130: Welcome Email Template](#step-130)

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


---

<a id="step-129"></a>

### Step 129: Connect Employee Form

#### Before this step

The employee form was written, but it had to be connected to the account-creation screen used at this stage. Immediately before this, Step 128 dealt with employee creation form; the work here continued the same part of the application.

#### What we did and why

Django Admin can use a custom add form when an administrator creates a user. This is an early onboarding route; later we build our own employee-creation page inside the application.

#### Code and commands

**Step 129, block 1.** This code sample or terminal command was used while working on connect employee form. The content below remains in its original sequence for Step 129, block 1.

```python
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import CustomUser
from .forms import EmployeeCreationForm


class CustomUserAdmin(UserAdmin):

    add_form = EmployeeCreationForm

    fieldsets = UserAdmin.fieldsets + (
        ('Inventory Role', {'fields': ('role',)}),
    )

    add_fieldsets = (
        (None, {
            'fields': (
                'username',
                'first_name',
                'last_name',
                'email',
                'role'
            )
        }),
    )


admin.site.register(CustomUser, CustomUserAdmin)
```

#### How the code works

**Block 1 (python, 28 lines).** In Step 129, connect employee form involved `accounts/admin.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib import admin` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.admin import UserAdmin` makes a Python function, class or module available to the code that follows. `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `from .forms import EmployeeCreationForm` makes a Python function, class or module available to the code that follows.

**Lines 8–14:** `add_form = EmployeeCreationForm` sets the named value used by the next operation in this file. `fieldsets = UserAdmin.fieldsets + (` sets the named value used by the next operation in this file.

**Lines 15–21:** `add_fieldsets = (` sets the named value used by the next operation in this file.

**Lines 22–28:** The lines starting with `'role'` supply the values or page content for this part of accounts/admin.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 129. For Step 129, the proposed way to check connect employee form was to revisit `accounts/admin.py` and run the corresponding command or feature to see its result. Development continued in Step 130 before a complete result could be claimed.

#### Next

[Step 128: Employee Creation Form](#step-128) · [Step 130: Welcome Email Template](#step-130)


---

<a id="step-130"></a>

### Step 130: Welcome Email Template

#### Before this step

A newly created employee needs to know why they received an email and what to do next. Immediately before this, Step 129 dealt with connect employee form; the work here continued the same part of the application.

#### What we did and why

The welcome-email template is the message body. It introduces the account and includes a link for setting the password rather than sending a password in plain text.

#### Code and commands

**Step 130, block 1.** This code sample or terminal command was used while working on welcome email template. The content below remains in its original sequence for Step 130, block 1.

```django
Hello {{ first_name }},

Welcome to the Inventory System.

Your employee account has been created.

Username: {{ username }}

To activate your account, set your password using the link below:

{{ setup_link }}

This link is temporary and can only be used once to set your password.

If you were not expecting this email, please contact your administrator.

Inventory System
```

#### How the code works

**Block 1 (django, 17 lines).** In Step 130, welcome email template involved `templates/accounts/welcome_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The lines starting with `Hello {{ first_name }},` supply the values or page content for this part of templates/accounts/welcome_email.txt. These lines are kept together because their order and indentation matter.

**Lines 8–14:** The lines starting with `To activate your account, set your password using the link below:` supply the values or page content for this part of templates/accounts/welcome_email.txt. These lines are kept together because their order and indentation matter.

**Lines 15–17:** The lines starting with `If you were not expecting this email, please contact your administrator.` supply the values or page content for this part of templates/accounts/welcome_email.txt. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 130. For Step 130, the proposed way to check welcome email template was to open the related page in the browser and check that the route reached the intended view or template in `templates/accounts/welcome_email.txt`. Development continued in Step 131 before a complete result could be claimed.

#### Next

[Step 129: Connect Employee Form](#step-129) · [Step 131: Secure Password-Setup Link](#step-131)
