# MODULE 1 — Employee accounts

## Chapter 3: Login (Steps 73–79)

### Before we begin

The administrator could use Django's built-in site, but ordinary employees needed their own login screen inside the inventory website.

### What we worked on

We built a login function, an HTML form and the URL rules that connect them. We also traced what happens when login sends the browser to the homepage.

### Picture it this way

A typical visit goes: browser opens login page, employee types credentials, Django checks them, then the browser is sent to the home address. The next chapter adds a way to log out.

### Find a step

- [Step 73: Login View](#step-73)

---

<a id="step-73"></a>

### Step 73: Login View

#### Before this step

Until now, the only built-in account screen was Django Admin. Employees needed a normal login page inside the inventory application. The preceding step, Step 72, covered register customuser, so this was the next part of the development sequence.

#### What we did and why

A view receives the browser's request, reads the submitted username and password, and asks Django to check them. If they are correct, Django signs the person in and redirects; if not, the form can explain that login failed.

#### Code and commands

**Step 73, block 1.** This code sample or terminal command was used while working on login view. The content below remains in its original sequence for Step 73, block 1.

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

#### How the code works

**Block 1 (python, 23 lines).** In Step 73, login view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.shortcuts import render, redirect` makes a Python function, class or module available to the code that follows. `from django.contrib.auth import authenticate, login` makes a Python function, class or module available to the code that follows. `from django.contrib import messages` makes a Python function, class or module available to the code that follows. `def loginpage(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Lines 8–14:** `username = request.POST.get('username')` sets the named value used by the next operation in this file. `password = request.POST.get('password')` sets the named value used by the next operation in this file. `user = authenticate(` sets the named value used by the next operation in this file. `username=username,` sets the named value used by the next operation in this file.

**Lines 15–21:** `if user is not None:` is a condition: the code inside this branch only runs when its condition is satisfied. `return redirect('/')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. `messages.error(request, 'Invalid username or password.')` prepares feedback that can appear when the user reaches the next page.

**Lines 22–23:** `return render(request, 'accounts/login.html')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 73. For Step 73, the proposed way to check login view was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 74 before a complete result could be claimed.

#### Next

[Step 72: Register CustomUser](../Module-1/02-database-setup-and-admin-site.md#step-72) · [Step 74: Understand models.py versus admin.py](#step-74)
