# Module 1, Chapter 3: Login

## Step 73: Login View

#### Next: Create the login function

Open `accounts/views.py` and replace its contents with:

```python
from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, login
from django.contrib import messages


def loginpage(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        user = authenticate(
            request,
            username=username,
            password=password
        )

        if user is not None:
            login(request, user)
            return redirect('/')

        messages.error(request, 'Invalid username or password.')

    return render(request, 'accounts/login.html')
```

**What it does:** Checks the username and password, logs in the user if valid, and redirects to the homepage.

This follows the login-function approach taught in class.

**Expected result:** The file saves without errors. We'll create the login page next.

*Previous: Step 72 (Register CustomUser)  |  Next: Step 74 (models.py vs admin.py)*

---
