# MODULE 1 — Accounts

## Chapter 11: Employee welcome email and password-setup link (Steps 128–135)

**Where we are.** A newly created employee needs a private setup link rather than a password disclosed by an administrator.

**What this chapter gives us.** Build employee forms, tokenized welcome emails, expiration behavior, and resend capability.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain how a one-time-style setup flow reuses Django password-reset mechanisms.

### Find a step

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

**Why we are doing this.** Administrators would need a form to create staff accounts without giving them access to raw Django Admin controls.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** `EmployeeCreationForm` defines permitted fields and handles employee account creation. Form validation happens before a new `CustomUser` row is saved.

**Follow the flow.** `EmployeeCreationForm` is a model form, so its `Meta` section states which `CustomUser` fields appear. Its `save` method asks Django to construct a user without immediately writing it, then calls `set_unusable_password`. The employee therefore cannot log in with an arbitrary default password; they first follow the secure email setup link.

**How we checked it.** A view is needed to show the form and save submitted data; that wiring appears later.

**Connects to.** Previous: [Step 127 — Email Testing](10-forgot-and-reset-password.md#step-127) · Next: [Step 129 — Connect Employee Form](11-employee-welcome-email-and-password-setup-link.md#step-129)


---

<a id="step-129"></a>

### Step 129: Connect Employee Form

**Why we are doing this.** The form alone did not yet define the full account onboarding experience, especially the initial password state.

**File / location.** `accounts/forms.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open accounts/admin.py and replace its entire contents with.*

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

**How it works.** The notes connect employee creation with safe initial-password handling and the existing user model. Later steps add a secure setup link instead of exposing an assigned password.

**Follow the flow.** The creation form from Step 128 was attached to the Django Admin user interface as well. `add_form` names the form; `add_fieldsets` chooses which fields Django Admin shows during account creation. This is distinct from the custom employee creation page built later.

**How we checked it.** The complete browser interface is built in Steps 139–143; this is an early form integration step.

**Connects to.** Previous: [Step 128 — Employee Creation Form](11-employee-welcome-email-and-password-setup-link.md#step-128) · Next: [Step 130 — Welcome Email Template](11-employee-welcome-email-and-password-setup-link.md#step-130)


---

<a id="step-130"></a>

### Step 130: Welcome Email Template

**Why we are doing this.** A newly created employee needs an understandable message explaining how to set their own password.

**File / location.** `templates/accounts/welcome_email.txt`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** The welcome email template greets the employee and includes a setup link. Keeping the content in a template avoids hardcoding the message in Python.

**How we checked it.** The secure tokenized link is assembled in Step 131.

**Connects to.** Previous: [Step 129 — Connect Employee Form](11-employee-welcome-email-and-password-setup-link.md#step-129) · Next: [Step 131 — Secure Password-Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-131)


---

<a id="step-131"></a>

### Step 131: Secure Password-Setup Link

**Why we are doing this.** A welcome email must not expose a permanent password or give unlimited access to account setup.

**File / location.** `accounts/utils.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

**How it works.** The helper constructs a password-setup URL using Django's token generator and encoded user identity. The recipient can set a password through the reset-confirm flow.

**Follow the flow.** `force_bytes(user.pk)` converts the database ID into bytes; `urlsafe_base64_encode` makes it suitable for a URL. The token generator produces a value Django can later validate. `reverse` builds the named password-reset-confirm path, and `build_absolute_uri` adds the site address so the link works from an email outside the app.

**How we checked it.** The link mechanism is integrated into sending in Step 132 and expiration is discussed later.

**Connects to.** Previous: [Step 130 — Welcome Email Template](11-employee-welcome-email-and-password-setup-link.md#step-130) · Next: [Step 132 — Send Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-132)


---

<a id="step-132"></a>

### Step 132: Send Welcome Email

**Why we are doing this.** After constructing the setup link, we needed an email-sending helper that combines recipient details with the template.

**File / location.** `accounts/utils.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these imports at the top.*

```python
from django.conf import settings
from django.core.mail import send_mail
from django.template.loader import render_to_string
```

*2. Recorded snippet; Then add this function below generatesetuplink().*

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

**How it works.** `send_welcome_email` prepares and sends the welcome message through Django's configured email backend, using the secure setup URL.

**Follow the flow.** The helper starts by obtaining the safe setup link. `render_to_string` fills the email template with the employee's name, username, and link. `send_mail` then passes the message, sender, and recipient to whichever email backend is configured. Console mode prints it; SMTP mode attempts delivery.

**How we checked it.** Automatic invocation from the employee-creation workflow is connected in Step 133.

**Connects to.** Previous: [Step 131 — Secure Password-Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-131) · Next: [Step 133 — Automatic Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-133)


---

<a id="step-133"></a>

### Step 133: Automatic Welcome Email

**Why we are doing this.** The administrator should not have to remember to send a welcome message after each employee is created.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this import at the top.*

```python
from .utils import send_welcome_email
```

*2. Recorded snippet; Inside CustomUserAdmin, below addfieldsets, add.*

```python
    def save_model(self, request, obj, form, change):
        super().save_model(request, obj, form, change)

        if not change:
            send_welcome_email(request, obj)
```

**How it works.** The create/save workflow invokes the email helper after successfully creating an account. If delivery fails, the account's creation state must be handled explicitly; later steps improve this behavior.

**Follow the flow.** Saving an employee and sending an email are different operations. Here, the create workflow calls the email helper after `form.save()`. It then sets a success message and redirects. A later step adds exception handling so a delivery failure cannot make the interface falsely imply that the employee was never created.

**How we checked it.** End-to-end testing requires the form, URL, and mail backend; those are completed at later steps.

**Connects to.** Previous: [Step 132 — Send Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-132) · Next: [Step 134 — Link Expiration](11-employee-welcome-email-and-password-setup-link.md#step-134)


---

<a id="step-134"></a>

### Step 134: Link Expiration

**Why we are doing this.** A password-setup link should not remain useful indefinitely, particularly if an email is forwarded or leaked.

**File / location.** `config/settings.py (token timeout)`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Open config/settings.py and add.*

```python
PASSWORD_RESET_TIMEOUT = 60 * 60 * 24
```

**How it works.** Django's password reset tokens depend on user state and a configured timeout. The notes explain the link's expiration rather than creating a separate new token system.

**How we checked it.** Check the configured timeout and test old tokens as part of security verification; no elapsed-time test is recorded here.

**Connects to.** Previous: [Step 133 — Automatic Welcome Email](11-employee-welcome-email-and-password-setup-link.md#step-133) · Next: [Step 135 — Resend Setup Link](11-employee-welcome-email-and-password-setup-link.md#step-135)


---

<a id="step-135"></a>

### Step 135: Resend Setup Link

**Why we are doing this.** Employees can miss or lose their first setup email, so administrators need a controlled way to issue another link.

**File / location.** `accounts/utils.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Inside the existing CustomUserAdmin class, add the following code below savemodel().*

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

**How it works.** The resend helper regenerates a suitable password-setup message for an existing account. The later employee management page provides the button and permission checks.

Email delivery is a separate operation from creating the employee record; a failure needs deliberate handling.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** The user interface for resending is added in Steps 160–162.

**Connects to.** Previous: [Step 134 — Link Expiration](11-employee-welcome-email-and-password-setup-link.md#step-134) · Next: [Step 136 — Employee Management](12-employee-list.md#step-136)

---

**Chapter recap.** Build employee forms, tokenized welcome emails, expiration behavior, and resend capability.

**Practice explaining:** Explain how a one-time-style setup flow reuses Django password-reset mechanisms.

