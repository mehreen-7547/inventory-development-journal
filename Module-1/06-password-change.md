# MODULE 1 — Employee accounts

## Chapter 6: Password change (Steps 86–90)

### Before we begin

Employees could enter their accounts, but could not yet change a password they already knew.

### What we worked on

We created a password-change view, linked it to a page and added the menu link that lets employees reach it.

### Picture it this way

The page first asks for the old password and the new one. Django checks the form before saving the change, then keeps the current session working as intended. This is different from forgotten-password recovery, which comes later.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 86: Change Password](#step-86)
- [Step 87: Walk through password change](#step-87)
- [Step 88: Password Change URL](#step-88)
- [Step 89: Password Change Form](#step-89)
- [Step 90: Change Password Link](#step-90)

---

<a id="step-86"></a>

### Step 86: Change Password

**What was happening before this step?**

Employees could sign in, but they had no normal way to change a password they already knew.

**What did we do, and what does it mean?**

The password-change view uses a Django form that asks for the old password and a new one. It checks the old password first, then saves the new one only if the form is valid.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add these imports.*

```python
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash
from django.contrib.auth.decorators import login_required
```

*2. Add this function at the bottom.*

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

**Understanding the code, one piece at a time**

`PasswordChangeForm` supplies the fields and checks for changing an existing password. `form.is_valid()` means Django accepted the entered values. Saving the form changes the password; `update_session_auth_hash(...)` helps keep the session consistent after the change.

**How did we know what happened?**

The page and URL are built in later steps, so this view was not yet testable end to end.

**Where to go next:** Previous: [Step 85 — Login Page Check](05-homepage-protection-and-navigation.md#step-85) · Next: [Step 87 — Walk through password change](06-password-change.md#step-87)


---

<a id="step-87"></a>

### Step 87: Walk through password change

**What was happening before this step?**

That view contained several steps in one function, so we slowed down and followed one browser request at a time.

**What did we do, and what does it mean?**

Opening the page normally is a GET request, so the view shows a blank form. Submitting it is POST, so the view checks the entered values. If the change succeeds, Django updates the login session so the user can stay signed in.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

**Understanding the code, one piece at a time**

`render(...)` combines a page template with data from the view and returns the result to the browser.

**How did we know what happened?**

This is a conceptual walkthrough. It does not represent eleven separate edits despite containing code examples.

**Where to go next:** Previous: [Step 86 — Change Password](06-password-change.md#step-86) · Next: [Step 88 — Password Change URL](06-password-change.md#step-88)


---

<a id="step-88"></a>

### Step 88: Password Change URL

**What was happening before this step?**

The password-change code existed, but it still needed a public address.

**What did we do, and what does it mean?**

A URL rule points to the password-change view. Naming the URL also lets templates create links without hard-coding a full address.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add the third path.*

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
    path('change-password/', views.changepassword, name='change_password'),
]
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The form template is the next missing part.

**Where to go next:** Previous: [Step 87 — Walk through password change](06-password-change.md#step-87) · Next: [Step 89 — Password Change Form](06-password-change.md#step-89)


---

<a id="step-89"></a>

### Step 89: Password Change Form

**What was happening before this step?**

The user needed to see the old-password and new-password fields in the browser.

**What did we do, and what does it mean?**

The HTML template displays the Django form, any field errors and a submit button. The view performs the checks; the template's job is to show the form clearly.

**File or place:** `templates/accounts/change_password.html`.

**Code or command from this step**

*1. Add this code.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

Verify with an incorrect old password and with valid new values after the homepage link is added.

**Where to go next:** Previous: [Step 88 — Password Change URL](06-password-change.md#step-88) · Next: [Step 90 — Change Password Link](06-password-change.md#step-90)


---

<a id="step-90"></a>

### Step 90: Change Password Link

**What was happening before this step?**

Even a working page is difficult to use if nobody can find it.

**What did we do, and what does it mean?**

We added a change-password link on the homepage. This is a navigation change, not another password-checking function.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Add this line above the Logout button.*

```html
<a href="{% url 'change_password' %}">Change Password</a>
```

**How did we know what happened?**

Once added, a logged-in user should be able to move from homepage to password-change form.

**Where to go next:** Previous: [Step 89 — Password Change Form](06-password-change.md#step-89) · Next: [Step 91 — Role-Based Homepage](07-role-based-homepage-and-feedback-messages.md#step-91)

---

### The point of this chapter

The page first asks for the old password and the new one. Django checks the form before saving the change, then keeps the current session working as intended. This is different from forgotten-password recovery, which comes later.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

