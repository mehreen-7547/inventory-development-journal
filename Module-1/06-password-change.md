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
