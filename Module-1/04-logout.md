# Module 1, Chapter 4: Logout

## Step 80: Logout Function

#### Next: Add logout functionality

Open `accounts/views.py`.

**1.** Change the existing authentication import to:

```python
from django.contrib.auth import authenticate, login, logout
```

**2.** Add this import:

```python
from django.views.decorators.http import require_POST
```

**3.** Add this function below `loginpage()`:

```python
@require_POST
def logoutpage(request):
    logout(request)
    return redirect('login')
```

**What it does:** Logs out the current user and redirects them to the login page.

  NEW vs CLASS
  **Where:** `accounts/views.py`

  **Class example:** `backend/secondproject/library/views.py` uses `logout(request)`.

  **Difference:** We added `@require_POST`.

  **Why:** Prevents logout through an ordinary link or GET request.

  **Alternative:** The class version without this restriction.

**Expected result:** File saves successfully. We'll connect the logout URL next.

*Previous: Step 79 (Project vs App)  |  Next: Step 81 (Logout URL)*

---

## Step 81: Logout URL

#### Connect the logout function to a URL

Open **`accounts/urls.py`**.

Add the logout path below the login path:

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
]
```

**What this means:**

- `/login/` → executes `loginpage()`
- `/logout/` → connects to `logoutpage()`

Because we used `@require_POST`, simply typing `/logout/` in the browser won't log you out.

**Expected result:** Save the file. Django should show no errors.

Next, we'll create the actual Logout button.

*Previous: Step 80 (Logout Function)  |  Next: Step 82 (Logout Button)*

---
