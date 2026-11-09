# MODULE 1 — Employee accounts

## Chapter 6: Password change (Steps 86–90)

### Before we begin

Employees could enter their accounts, but could not yet change a password they already knew.

### What we worked on

We created a password-change view, linked it to a page and added the menu link that lets employees reach it.

### Picture it this way

The page first asks for the old password and the new one. Django checks the form before saving the change, then keeps the current session working as intended. This is different from forgotten-password recovery, which comes later.

### Find a step

- [Step 86: Change Password](#step-86)
- [Step 87: Walk through password change](#step-87)
- [Step 88: Password Change URL](#step-88)
- [Step 89: Password Change Form](#step-89)

---

<a id="step-86"></a>

### Step 86: Change Password

#### Before this step

Employees could sign in, but they had no normal way to change a password they already knew. The preceding step, Step 85, covered login page check, so this was the next part of the development sequence.

#### What we did and why

The password-change view uses a Django form that asks for the old password and a new one. It checks the old password first, then saves the new one only if the form is valid.

#### Code and commands

**Step 86, block 1.** This code sample or terminal command was used while working on change password. The content below remains in its original sequence for Step 86, block 1.

```python
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash
from django.contrib.auth.decorators import login_required
```

**Step 86, block 2.** This code sample or terminal command was used while working on change password. The content below remains in its original sequence for Step 86, block 2.

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

#### How the code works

**Block 1 (python, 3 lines).** In Step 86, change password involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `from django.contrib.auth.forms import PasswordChangeForm` makes a Python function, class or module available to the code that follows. `from django.contrib.auth import update_session_auth_hash` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.decorators import login_required` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 13 lines).** In Step 86, change password involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `def changepassword(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied. `form = PasswordChangeForm(request.user, request.POST)` sets the named value used by the next operation in this file.

**Lines 8–13:** `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. The `else` branch handles the alternative case when the preceding condition was not satisfied. `form = PasswordChangeForm(request.user)` sets the named value used by the next operation in this file. `return render(request, 'accounts/change_password.html', {'form': form})` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 86. For Step 86, the proposed way to check change password was to open the password-related page and try the expected form flow with a test account. Development continued in Step 87 before a complete result could be claimed.

#### Next

[Step 85: Login Page Check](../Module-1/05-homepage-protection-and-navigation.md#step-85) · [Step 87: Walk through password change](#step-87)


---

<a id="step-87"></a>

### Step 87: Walk through password change

#### Before this step

That view contained several steps in one function, so we slowed down and followed one browser request at a time. Immediately before this, Step 86 dealt with change password; the work here continued the same part of the application.

#### What we did and why

Opening the page normally is a GET request, so the view shows a blank form. Submitting it is POST, so the view checks the entered values. If the change succeeds, Django updates the login session so the user can stay signed in.

#### Code and commands

**Step 87, block 1.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 1.

```python
@login_required(login_url='/login/')
def changepassword(request):
```

**Step 87, block 2.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 2.

```python
if request.method == 'POST':
```

**Step 87, block 3.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 3.

```python
else:
    form = PasswordChangeForm(request.user)
```

**Step 87, block 4.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 4.

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

**Step 87, block 5.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 5.

```python
if request.method == 'POST':
```

**Step 87, block 6.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 6.

```python
form = PasswordChangeForm(request.user, request.POST)
```

**Step 87, block 7.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 7.

```python
if form.is_valid():
```

**Step 87, block 8.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 8.

```python
user = form.save()
```

**Step 87, block 9.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 9.

```python
update_session_auth_hash(request, user)
```

**Step 87, block 10.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 10.

```python
return redirect('home')
```

**Step 87, block 11.** This code sample or terminal command was used while working on walk through password change. The content below remains in its original sequence for Step 87, block 11.

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

#### How the code works

**Block 1 (python, 2 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `def changepassword(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 2 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied.

**Block 3 (python, 2 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The `else` branch handles the alternative case when the preceding condition was not satisfied. `form = PasswordChangeForm(request.user)` sets the named value used by the next operation in this file.

**Block 4 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return render(request, 'accounts/change_password.html', {'form': form})` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 5 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `if request.method == 'POST':` is a condition: the code inside this branch only runs when its condition is satisfied.

**Block 6 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `form = PasswordChangeForm(request.user, request.POST)` sets the named value used by the next operation in this file.

**Block 7 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `if form.is_valid():` is a condition: the code inside this branch only runs when its condition is satisfied.

**Block 8 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `form.save()` writes the validated form values into the corresponding database record.

**Block 9 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `update_session_auth_hash(request, user)` supply the values or page content for this part of accounts/change_password.html. These lines are kept together because their order and indentation matter.

**Block 10 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 11 (python, 1 lines).** In Step 87, walk through password change involved `accounts/change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return render(request, 'accounts/change_password.html', {'form': form})` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 87. For Step 87, the proposed way to check walk through password change was to open the password-related page and try the expected form flow with a test account. Development continued in Step 88 before a complete result could be claimed.

#### Next

[Step 86: Change Password](#step-86) · [Step 88: Password Change URL](#step-88)


---

<a id="step-88"></a>

### Step 88: Password Change URL

#### Before this step

The password-change code existed, but it still needed a public address. Immediately before this, Step 87 dealt with walk through password change; the work here continued the same part of the application.

#### What we did and why

A URL rule points to the password-change view. Naming the URL also lets templates create links without hard-coding a full address.

#### Code and commands

**Step 88, block 1.** This code sample or terminal command was used while working on password change url. The content below remains in its original sequence for Step 88, block 1.

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
    path('change-password/', views.changepassword, name='change_password'),
]
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 88, password change url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `urlpatterns = [` sets the named value used by the next operation in this file. `path('login/', views.loginpage, name='login'),` begins a URL rule connecting a browser address with the function that answers it. `path('logout/', views.logoutpage, name='logout'),` begins a URL rule connecting a browser address with the function that answers it. `path('change-password/', views.changepassword, name='change_password'),` begins a URL rule connecting a browser address with the function that answers it.

#### How we checked it

Not tested yet at Step 88. For Step 88, the proposed way to check password change url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Development continued in Step 89 before a complete result could be claimed.

#### Next

[Step 87: Walk through password change](#step-87) · [Step 89: Password Change Form](#step-89)


---

<a id="step-89"></a>

### Step 89: Password Change Form

#### Before this step

The user needed to see the old-password and new-password fields in the browser. Immediately before this, Step 88 dealt with password change url; the work here continued the same part of the application.

#### What we did and why

The HTML template displays the Django form, any field errors and a submit button. The view performs the checks; the template's job is to show the form clearly.

#### Code and commands

**Step 89, block 1.** This code sample or terminal command was used while working on password change form. The content below remains in its original sequence for Step 89, block 1.

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

#### How the code works

**Block 1 (html, 16 lines).** In Step 89, password change form involved `login.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Change Password</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `<button type="submit">Change Password</button>` creates or closes part of the page structure seen by the browser.

**Lines 15–16:** The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 89. For Step 89, the proposed way to check password change form was to open the password-related page and try the expected form flow with a test account. Development continued in Step 90 before a complete result could be claimed.

#### Next

[Step 88: Password Change URL](#step-88) · [Step 90: Change Password Link](#step-90)
