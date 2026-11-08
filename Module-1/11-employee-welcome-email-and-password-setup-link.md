# Module 1, Chapter 11: Employee welcome email and password-setup link

## Step 128: Employee Creation Form

Your `admin.py` currently uses Django's default user-creation form, which includes password fields.

We need to change that so **the administrator creates an employee account without choosing the employee's password**.

#### Next: Create a custom employee form

Create a new file:

`accounts/forms.py`

Paste:

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

#### What does this code do?

- `ModelForm` — creates a form based on our `CustomUser` model.
- `fields` — specifies which details the administrator enters.
- `set_unusable_password()` — prevents the employee from logging in with a password until they set one through a secure setup link.
- `save()` — saves the employee account.

  **NEW vs CLASS — 2 concepts**

  `ModelForm` builds on the Django forms we've already used.

  `set_unusable_password()` is a built-in Django user-model method. It does not create a temporary password.

**For now:** Save `forms.py`. Don't change `admin.py` yet.

Next, we'll connect this form to Django Admin so the employee-creation page no longer asks the administrator to enter a password.

*Previous: Step 127 (Email Testing)  |  Next: Step 129 (Connect Employee Form)*

---

## Step 129: Connect Employee Form

#### Next: Connect our employee form to Django Admin

Open **`accounts/admin.py`** and replace its entire contents with:

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

#### What changed?

- `add_form` tells Django Admin to use our custom employee-creation form.
- `add_fieldsets` specifies the fields displayed when creating an employee.
- `fieldsets` preserves the existing configuration for editing users.

  **NEW vs CLASS — `add_form`**

  `add_form` is a built-in configuration attribute of Django's `UserAdmin`. We assign our `EmployeeCreationForm` to it.

#### Test

1. Save `admin.py`.
2. Open `http://127.0.0.1:8000/admin/`.
3. Navigate to **Users → Add user**.

**Expected:** You should see username, first name, last name, email and role — without password fields.

Don't create an employee yet. We still need to implement the welcome email and password-setup link.

*Previous: Step 128 (Employee Creation Form)  |  Next: Step 130 (Welcome Email Template)*

---
