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
- [Step 131: Secure Password-Setup Link](#step-131)
- [Step 132: Send Welcome Email](#step-132)
- [Step 133: Automatic Welcome Email](#step-133)

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


---

<a id="step-131"></a>

### Step 131: Secure Password-Setup Link

#### Before this step

The welcome email needed a link that belongs only to that employee and does not stay valid forever. Immediately before this, Step 130 dealt with welcome email template; the work here continued the same part of the application.

#### What we did and why

A helper function builds a link using the employee's record and a Django-generated token. Django checks the token later, so simply guessing an employee ID is not enough to set their password.

#### Code and commands

**Step 131, block 1.** This code sample or terminal command was used while working on secure password-setup link. The content below remains in its original sequence for Step 131, block 1.

```python
from django.contrib.auth.tokens import default_token_generator
from django.urls import reverse
from django.utils.encoding import force_bytes
from django.utils.http import urlsafe_base64_encode


def generate_setup_link(request, user):
    uid = urlsafe_base64_encode(force_bytes(user.pk))
    token = default_token_generator.make_token(user)

    path = reverse(
        'password_reset_confirm',
        kwargs={'uidb64': uid, 'token': token}
    )

    return request.build_absolute_uri(path)
```

#### How the code works

**Block 1 (python, 16 lines).** In Step 131, secure password-setup link involved `welcome_email.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib.auth.tokens import default_token_generator` makes a Python function, class or module available to the code that follows. `from django.urls import reverse` makes a Python function, class or module available to the code that follows. `from django.utils.encoding import force_bytes` makes a Python function, class or module available to the code that follows. `from django.utils.http import urlsafe_base64_encode` makes a Python function, class or module available to the code that follows.

**Lines 8–14:** `uid = urlsafe_base64_encode(force_bytes(user.pk))` sets the named value used by the next operation in this file. `token = default_token_generator.make_token(user)` sets the named value used by the next operation in this file. `path = reverse(` sets the named value used by the next operation in this file. `kwargs={'uidb64': uid, 'token': token}` sets the named value used by the next operation in this file.

**Lines 15–16:** `return request.build_absolute_uri(path)` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 131. For Step 131, the proposed way to check secure password-setup link was to open the password-related page and try the expected form flow with a test account. Development continued in Step 132 before a complete result could be claimed.

#### Next

[Step 130: Welcome Email Template](#step-130) · [Step 132: Send Welcome Email](#step-132)


---

<a id="step-132"></a>

### Step 132: Send Welcome Email

#### Before this step

We could build the link. We still needed one place in the code that prepares and sends the complete welcome message. Immediately before this, Step 131 dealt with secure password-setup link; the work here continued the same part of the application.

#### What we did and why

The helper fills the email template with the employee's details and passes it to Django's mail function. Whether the result appears in the terminal or goes to Gmail depends on the email setting chosen elsewhere.

#### Code and commands

**Step 132, block 1.** This code sample or terminal command was used while working on send welcome email. The content below remains in its original sequence for Step 132, block 1.

```python
from django.conf import settings
from django.core.mail import send_mail
from django.template.loader import render_to_string
```

**Step 132, block 2.** This code sample or terminal command was used while working on send welcome email. The content below remains in its original sequence for Step 132, block 2.

```python
def send_welcome_email(request, user):
    setup_link = generate_setup_link(request, user)

    message = render_to_string(
        'accounts/welcome_email.txt',
        {
            'first_name': user.first_name or user.username,
            'username': user.username,
            'setup_link': setup_link
        }
    )

    send_mail(
        'Welcome to Inventory System',
        message,
        settings.DEFAULT_FROM_EMAIL,
        [user.email]
    )
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 132, send welcome email involved `accounts/utils.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `from django.conf import settings` makes a Python function, class or module available to the code that follows. `from django.core.mail import send_mail` makes a Python function, class or module available to the code that follows. `from django.template.loader import render_to_string` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 18 lines).** In Step 132, send welcome email involved `accounts/utils.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `def send_welcome_email(request, user):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `setup_link = generate_setup_link(request, user)` sets the named value used by the next operation in this file. `message = render_to_string(` sets the named value used by the next operation in this file.

**Lines 8–14:** The lines starting with `'username': user.username,` supply the values or page content for this part of accounts/utils.py. These lines are kept together because their order and indentation matter.

**Lines 15–18:** `settings.DEFAULT_FROM_EMAIL,` reads one of the Django configuration values used by this feature.

#### How we checked it

Not tested yet at Step 132. For Step 132, the proposed way to check send welcome email was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 133 before a complete result could be claimed.

#### Next

[Step 131: Secure Password-Setup Link](#step-131) · [Step 133: Automatic Welcome Email](#step-133)


---

<a id="step-133"></a>

### Step 133: Automatic Welcome Email

#### Before this step

An administrator should not have to send the welcome email manually after creating each employee. Immediately before this, Step 132 dealt with send welcome email; the work here continued the same part of the application.

#### What we did and why

The account-creation workflow calls the email helper after saving the account. Saving and sending are two different actions; a later step adds clearer handling if sending fails.

#### Code and commands

**Step 133, block 1.** This code sample or terminal command was used while working on automatic welcome email. The content below remains in its original sequence for Step 133, block 1.

```python
from .utils import send_welcome_email
```

**Step 133, block 2.** This code sample or terminal command was used while working on automatic welcome email. The content below remains in its original sequence for Step 133, block 2.

```python
    def save_model(self, request, obj, form, change):
        super().save_model(request, obj, form, change)

        if not change:
            send_welcome_email(request, obj)
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 133, automatic welcome email involved `accounts/admin.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from .utils import send_welcome_email` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 5 lines).** In Step 133, automatic welcome email involved `accounts/admin.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `def save_model(self, request, obj, form, change):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if not change:` is a condition: the code inside this branch only runs when its condition is satisfied.

#### How we checked it

Not tested yet at Step 133. For Step 133, the proposed way to check automatic welcome email was to start Django Shell, inspect the email settings and run a safe test send. Development continued in Step 134 before a complete result could be claimed.

#### Next

[Step 132: Send Welcome Email](#step-132) · [Step 134: Link Expiration](#step-134)
