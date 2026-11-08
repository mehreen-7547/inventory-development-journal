# Module 1, Chapter 5: Homepage protection and navigation

## Step 83: Protect Homepage

#### Next: Allow only logged-in users to access the homepage

Currently, someone could open `/` without logging in.

Open **`core/views.py`** and update it:

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login/')
def home(request):
    return render(request, 'core/home.html')
```

**What does `@login_required` do?**

Before executing `home()`, Django checks whether the visitor is logged in.

- **Logged in:** Display the homepage.
- **Not logged in:** Redirect to `/login/`.

This is called a **decorator**, just like `@require_POST`. It adds a condition to a function.

**Test:** Log out, then manually open `http://127.0.0.1:8000/`. You should be redirected to the login page.

*Previous: Step 82 (Logout Button)  |  Next: Step 84 (Display User Details)*

---
