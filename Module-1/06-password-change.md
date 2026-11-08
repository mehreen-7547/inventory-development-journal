# Module 1, Chapter 6: Password change

## Step 86: Change Password

#### Next: Create the change-password function

We want logged-in users to be able to change their own passwords.

Open **`accounts/views.py`**.

**Add these imports:**

```python
from django.contrib.auth.forms import PasswordChangeForm
from django.contrib.auth import update_session_auth_hash
from django.contrib.auth.decorators import login_required
```

**Add this function at the bottom:**

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

**In simple terms:**

- `PasswordChangeForm` — Django's ready-made form for changing passwords.
- `form.is_valid()` — checks whether the old password is correct and the new password meets the rules.
- `form.save()` — saves the new password securely.
- `update_session_auth_hash()` — keeps the user logged in after changing their password.

  NEW vs CLASS
  **Where:** `accounts/views.py`

  **Class reference:** `backend/secondproject/library/views.py` uses function-based authentication views.

  **Difference:** Our password-change function uses Django's built-in `PasswordChangeForm` and session-update helper, which weren't present in the training views examined.

  **Why:** To handle password changes safely without writing our own password-validation logic.

  **Alternative:** Django's built-in `PasswordChangeView`, which uses a class-based view.

**Expected result:** Save the file without errors. We'll create its URL and HTML page next.

*Previous: Step 85 (Login Page Check)  |  Next: Step 87 (Code Walkthrough)*

---

## Step 87: Code Walkthrough

Imagine **manager1** wants to change their password.

We'll follow what happens from the moment they open the password-change page.

#### Scene 1 — Manager1 opens the page

```python
@login_required(login_url='/login/')
def changepassword(request):
```

Django first checks: **Is manager1 logged in?**

- Yes → Continue.
- No → Send them to `/login/`.

Now Django enters the `changepassword()` function.

```python
if request.method == 'POST':
```

Django asks: **Has manager1 submitted the form?**

Not yet! They have only opened the page, so the request is GET.

Django skips the `if` block and goes to `else`.

```python
else:
    form = PasswordChangeForm(request.user)
```

Django prepares an empty password-change form for manager1.

  What manager1 will see

    Old password

    New password

    Confirm new password

  Change Password
  Illustration only — we'll build the actual HTML form next.

Then:

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

Django displays the HTML page and gives it the form we just prepared.

#### Scene 2 — Manager1 submits the form

Manager1 enters their old password, chooses a new password, and clicks **Change Password**.

The browser sends those details to Django using POST.

The same function runs again, but this time:

```python
if request.method == 'POST':
```

The answer is **Yes**.

```python
form = PasswordChangeForm(request.user, request.POST)
```

Django creates a form using:

- `request.user` → manager1, whose password is being changed.
- `request.POST` → the password details submitted by manager1.

Now:

```python
if form.is_valid():
```

Django checks whether the old password is correct, the new passwords match, and the new password satisfies the password rules.

#### Scene 3 — The password is valid

```python
user = form.save()
```

Django securely saves manager1's new password.

```python
update_session_auth_hash(request, user)
```

Normally, changing a password can invalidate the current login session.

This line updates the session so **manager1 stays logged in**.

```python
return redirect('home')
```

Finally, Django sends manager1 back to the homepage.

#### But what if the password is wrong?

If `form.is_valid()` returns `False`, Django skips the password-saving code.

It reaches:

```python
return render(request, 'accounts/change_password.html', {'form': form})
```

The form is displayed again, this time containing validation errors so manager1 can correct them.

---

#### The entire story in one diagram

    **Manager1 opens Change Password**

    Is manager1 logged in?

        No

        Login page

        Yes

        Show empty form (GET)

    **Manager1 submits form (POST)**

    Are the password details valid?

        No

          Show form with errors

        Yes

          Save password → Keep logged in → Home

**The key idea:** This single function handles two situations: **GET displays the form; POST processes the submitted form.**

*Previous: Step 86 (Change Password)  |  Next: Step 88 (Password Change URL)*

---

## Step 88: Password Change URL

#### Next: Connect the password-change function to a URL

Open **`accounts/urls.py`**.

Add the third path:

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
    path('change-password/', views.changepassword, name='change_password'),
]
```

**What this means:**

When someone visits `/change-password/`, Django calls the `changepassword()` function in `accounts/views.py`.

That function will eventually display our password-change form.

**Expected result:** Save the file. There should be no errors.

Don't open the URL yet. We haven't created its HTML template.

*Previous: Step 87 (Code Walkthrough)  |  Next: Step 89 (Password Change Form)*

---
