# MODULE 1 — Employee accounts

## Chapter 11: Employee welcome email and password-setup link (Steps 128–135)

### Before we begin

An administrator could create employee accounts in Django Admin, but employees should not receive a password chosen by someone else.

### What we worked on

We prepared the employee form, welcome email and secure first-time password link. We also considered link expiry and the ability to resend an invitation.

### Picture it this way

Creating an employee and letting the employee choose a password are separate moments. The account can exist before that first password is set. The welcome message connects those moments.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 128: Employee Creation Form](#step-128)
- [Step 129: Connect Employee Form](#step-129)
- [Step 130: Welcome Email Template](#step-130)
- [Step 131: Secure Password-Setup Link](#step-131)
- [Step 132: Send Welcome Email](#step-132)
- [Step 133: Automatic Welcome Email](#step-133)
- [Step 134: Link Expiration](#step-134)
- [Step 135: Resend Setup Link](#step-135)

---

<a id="step-128"></a>

### Step 128: Employee Creation Form

**What was happening before this step?**

Administrators needed to create employee accounts, but employees should choose their own passwords.

**What did we do, and what does it mean?**

The creation form records details such as name, email and role while leaving the new account without a usable password. A separate email link will later let that employee choose a private password.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

In an employee creation form, `form.save(commit=False)` prepares a user object without saving it right away. Then `set_unusable_password()` makes sure it has no working password yet. Only after that do we save the new account. The employee later sets a password using a secure link.

**How did we know what happened?**

A view is needed to show the form and save submitted data; that wiring appears later.

**Where to go next:** Previous: [Step 127 — Email Testing](10-forgot-and-reset-password.md#step-127) · Next: [Step 129 — Connect Employee Form](11-employee-welcome-email-and-password-setup-link.md#step-129)


---

<a id="step-129"></a>

### Step 129: Connect Employee Form

**What was happening before this step?**

The employee form was written, but it had to be connected to the account-creation screen used at this stage.

**What did we do, and what does it mean?**

Django Admin can use a custom add form when an administrator creates a user. This is an early onboarding route; later we build our own employee-creation page inside the application.

**File or place:** `accounts/forms.py`.

**Code or command from this step**

*1. Open accounts/admin.py and replace its entire contents with.*

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

**How did we know what happened?**

The complete browser interface is built in Steps 139–143; this is an early form integration step.

**Where to go next:** Previous: [Step 128 — Employee Creation Form](11-employee-welcome-email-and-password-setup-link.md#step-128) · Next: [Step 130 — Welcome Email Template](11-employee-welcome-email-and-password-setup-link.md#step-130)


---

<a id="step-130"></a>

### Step 130: Welcome Email Template

**What was happening before this step?**

A newly created employee needs to know why they received an email and what to do next.

**What did we do, and what does it mean?**

The welcome-email template is the message body. It introduces the account and includes a link for setting the password rather than sending a password in plain text.

**File or place:** `templates/accounts/welcome_email.txt`.

**Code or command from this step**

*1. Paste.*

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

**How did we know what happened?**

The secure tokenized link is assembled in Step 131.

**Where to go next:** Previous: [Step 129 — Connect Employee Form](11-employee-welcome-email-and-password-setup-link.md#step-129) · Next: [Step 131 — Secure Password-Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-131)


---

<a id="step-131"></a>

### Step 131: Secure Password-Setup Link

**What was happening before this step?**

The welcome email needed a link that belongs only to that employee and does not stay valid forever.

**What did we do, and what does it mean?**

A helper function builds a link using the employee's record and a Django-generated token. Django checks the token later, so simply guessing an employee ID is not enough to set their password.

**File or place:** `accounts/utils.py`.

**Code or command from this step**

*1. Paste.*

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

**Understanding the code, one piece at a time**

A helper starts with the employee's account ID and asks Django to generate a temporary token. It then builds a link to the password-setting view. A full address is needed because the recipient will click it from an email, not from inside the website.

**How did we know what happened?**

The link mechanism is integrated into sending in Step 132 and expiration is discussed later.

**Where to go next:** Previous: [Step 130 — Welcome Email Template](11-employee-welcome-email-and-password-setup-link.md#step-130) · Next: [Step 132 — Send Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-132)


---

<a id="step-132"></a>

### Step 132: Send Welcome Email

**What was happening before this step?**

We could build the link. We still needed one place in the code that prepares and sends the complete welcome message.

**What did we do, and what does it mean?**

The helper fills the email template with the employee's details and passes it to Django's mail function. Whether the result appears in the terminal or goes to Gmail depends on the email setting chosen elsewhere.

**File or place:** `accounts/utils.py`.

**Code or command from this step**

*1. Add these imports at the top.*

```python
from django.conf import settings
from django.core.mail import send_mail
from django.template.loader import render_to_string
```

*2. Then add this function below generatesetuplink().*

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

**Understanding the code, one piece at a time**

The email helper first prepares a link, then fills the message template with employee details, then asks Django to send it. The helper can be reused both when an account is first created and when a new invitation is requested.

**How did we know what happened?**

Automatic invocation from the employee-creation workflow is connected in Step 133.

**Where to go next:** Previous: [Step 131 — Secure Password-Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-131) · Next: [Step 133 — Automatic Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-133)


---

<a id="step-133"></a>

### Step 133: Automatic Welcome Email

**What was happening before this step?**

An administrator should not have to send the welcome email manually after creating each employee.

**What did we do, and what does it mean?**

The account-creation workflow calls the email helper after saving the account. Saving and sending are two different actions; a later step adds clearer handling if sending fails.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add this import at the top.*

```python
from .utils import send_welcome_email
```

*2. Inside CustomUserAdmin, below addfieldsets, add.*

```python
    def save_model(self, request, obj, form, change):
        super().save_model(request, obj, form, change)

        if not change:
            send_welcome_email(request, obj)
```

**Understanding the code, one piece at a time**

Notice the order: the account is saved first, then an email is attempted. These are two tasks. If Gmail has a problem, it does not automatically mean the database save failed.

**How did we know what happened?**

End-to-end testing requires the form, URL, and mail backend; those are completed at later steps.

**Where to go next:** Previous: [Step 132 — Send Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-132) · Next: [Step 134 — Link Expiration](11-employee-welcome-email-and-password-setup-link.md#step-134)


---

<a id="step-134"></a>

### Step 134: Link Expiration

**What was happening before this step?**

A password-setup link should not work forever if somebody finds an old email.

**What did we do, and what does it mean?**

Django lets us set how long reset tokens remain useful. We reused that mechanism for first-time password setup so old links stop working after the configured period.

**File or place:** `config/settings.py (token timeout)`.

**Code or command from this step**

*1. Open config/settings.py and add.*

```python
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

**How did we know what happened?**

Check the configured timeout and test old tokens as part of security verification; no elapsed-time test is recorded here.

**Where to go next:** Previous: [Step 133 — Automatic Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-133) · Next: [Step 135 — Resend Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-135)


---

<a id="step-135"></a>

### Step 135: Resend Setup Link

**What was happening before this step?**

A person might lose their first email or let its link expire, but we should not create another account for them.

**What did we do, and what does it mean?**

The resend action prepares a fresh setup link for an eligible existing employee. It reuses the same user record rather than duplicating people in the database.

**File or place:** `accounts/utils.py`.

**Code or command from this step**

*1. Inside the existing CustomUserAdmin class, add the following code below savemodel().*

```python
    actions = ['resend_setup_email']

    @admin.action(description='Resend password setup email')
    def resend_setup_email(self, request, queryset):
        count = 0

        for user in queryset:
            if user.is_active and not user.has_usable_password() and user.email:
                send_welcome_email(request, user)
                count += 1

        self.message_user(
            request,
            f'Setup email generated for {count} employee(s).'
        )
```

**How did we know what happened?**

The user interface for resending is added in Steps 160–162.

**Where to go next:** Previous: [Step 134 — Link Expiration](11-employee-welcome-email-and-password-setup-link.md#step-134) · Next: [Step 136 — Employee Management](12-employee-list.md#step-136)

---

### The point of this chapter

Creating an employee and letting the employee choose a password are separate moments. The account can exist before that first password is set. The welcome message connects those moments.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

