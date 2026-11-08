# MODULE 1 — Accounts

## Chapter 6: Password change (Steps 86–90)

**Where we are.** Staff should be able to change their own password without an administrator doing it for them.

**What this chapter gives us.** Understand PasswordChangeForm, current-session preservation, the route, and the HTML form.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why update_session_auth_hash is important after changing a password.

### Find a step

- [Step 86: Change Password](#step-86)
- [Step 87: Walk through password change](#step-87)
- [Step 88: Password Change URL](#step-88)
- [Step 89: Password Change Form](#step-89)
- [Step 90: Change Password Link](#step-90)

---

<a id="step-86"></a>

### Step 86: Change Password

**Why we are doing this.** The application needed a self-service way for signed-in staff to replace their current password.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these imports.*

```python
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash
from django.contrib.auth.decorators import login_required
```

*2. Recorded snippet; Add this function at the bottom.*

```python
@login_required(login_url='/login/')
def changepassword(request):
    if request.method == 'POST':
        form = PasswordChangeForm(request.user, request.POST)

        if form.is_valid():
            user = form.save()
            update_session_auth_hash(request, user)
            return redirect('home')
    else:
        form = PasswordChangeForm(request.user)

    return render(request, 'accounts/change_password.html', {'form': form})
```

**How it works.** `PasswordChangeForm` checks the old password and validates the new one. After saving, `update_session_auth_hash` keeps the current login session valid instead of signing the user out.

**Follow the flow.** The GET request creates a `PasswordChangeForm` for the currently signed-in user and shows the page. On POST, Django binds the old and new password fields to the form. If valid, `form.save()` hashes and stores the new password. `update_session_auth_hash` refreshes the session so changing a password does not log the same user out. Invalid submissions redisplay errors.

**How we checked it.** The page and URL are built in later steps, so this view was not yet testable end to end.

**Connects to.** Previous: [Step 85 — Login Page Check](05-homepage-protection-and-navigation.md#step-85) · Next: [Step 87 — Walk through password change](06-password-change.md#step-87)


---

<a id="step-87"></a>

### Step 87: Walk through password change

**Why we are doing this.** The password-change view contains several related concepts that are easy to memorize without understanding: decorator, GET, POST, form, and session hash.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

**How it works.** The walkthrough follows an example user through opening the page, submitting a form, validation, password saving, and the final redirect. It explains the same view from Step 86.

**How we checked it.** This is a conceptual walkthrough. It does not represent eleven separate edits despite containing code examples.

**Connects to.** Previous: [Step 86 — Change Password](06-password-change.md#step-86) · Next: [Step 88 — Password Change URL](06-password-change.md#step-88)


---

<a id="step-88"></a>

### Step 88: Password Change URL

**Why we are doing this.** The password-change function still had no public route from the accounts URL configuration.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add the third path.*

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
    path('change-password/', views.changepassword, name='change_password'),
]
```

**How it works.** The named URL `/change-password/` connects to `changepassword` and can be used in templates with `{% url ... %}`.

**How we checked it.** The form template is the next missing part.

**Connects to.** Previous: [Step 87 — Walk through password change](06-password-change.md#step-87) · Next: [Step 89 — Password Change Form](06-password-change.md#step-89)


---

<a id="step-89"></a>

### Step 89: Password Change Form

**Why we are doing this.** The password-change view needs an HTML form to show old/new password fields and validation errors.

**File / location.** `templates/accounts/change_password.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this code.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Change Password</title>
</head>
<body>
    <h2>Change Password</h2>

    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}

        <button type="submit">Change Password</button>
    </form>
</body>
</html>
```

**How it works.** The template sends a CSRF-protected POST and renders Django form fields and messages. Errors are returned on the same page instead of saving invalid input.

**Follow the flow.** The view sends a `form` object into the template context. `{{ form.as_p }}` renders its fields and validation messages. The browser sends the completed fields back by POST with a CSRF token. Django checks the data in `PasswordChangeForm`; the template never decides whether the password is correct.

**How we checked it.** Verify with an incorrect old password and with valid new values after the homepage link is added.

**Connects to.** Previous: [Step 88 — Password Change URL](06-password-change.md#step-88) · Next: [Step 90 — Change Password Link](06-password-change.md#step-90)


---

<a id="step-90"></a>

### Step 90: Change Password Link

**Why we are doing this.** Without navigation, users would have to type the password-change URL manually.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this line above the Logout button.*

```html
<a href="{% url 'change_password' %}">Change Password</a>
```

**How it works.** The homepage link uses Django's named URL resolver to open the form without hardcoding the route.

**How we checked it.** Once added, a logged-in user should be able to move from homepage to password-change form.

**Connects to.** Previous: [Step 89 — Password Change Form](06-password-change.md#step-89) · Next: [Step 91 — Role-Based Homepage](07-role-based-homepage-and-feedback-messages.md#step-91)

---

**Chapter recap.** Understand PasswordChangeForm, current-session preservation, the route, and the HTML form.

**Practice explaining:** Explain why update_session_auth_hash is important after changing a password.

