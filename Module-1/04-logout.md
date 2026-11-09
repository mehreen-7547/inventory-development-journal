# MODULE 1 — Employee accounts

## Chapter 4: Logout (Steps 80–82)

### Before we begin

Users could log in, but there was no way to end the signed-in session from the website.

### What we worked on

We added the logout function, connected a URL to it and placed a logout control on the homepage.

### Picture it this way

Signing in creates a session so Django remembers who you are. Logging out ends that session. A link or button by itself does not end it; the request must reach the logout view.

### Find a step

- [Step 80: Logout Function](#step-80)
- [Step 81: Logout URL](#step-81)

---

<a id="step-80"></a>

### Step 80: Logout Function

#### Before this step

We could sign in, but we also needed a safe way to sign out. The preceding step, Step 79, covered understand a django project versus an app, so this was the next part of the development sequence.

#### What we did and why

Logout tells Django to end the current login session. The session is what lets the website remember the signed-in person across multiple pages. Ending it means the next protected request needs a login again.

#### Code and commands

**Step 80, block 1.** This code sample or terminal command was used while working on logout function. The content below remains in its original sequence for Step 80, block 1.

```python
from django.contrib.auth import authenticate, login, logout
```

**Step 80, block 2.** This code sample or terminal command was used while working on logout function. The content below remains in its original sequence for Step 80, block 2.

```python
from django.views.decorators.http import require_POST
```

**Step 80, block 3.** This code sample or terminal command was used while working on logout function. The content below remains in its original sequence for Step 80, block 3.

```python
@require_POST
def logoutpage(request):
    logout(request)
    return redirect('login')
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 80, logout function involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.contrib.auth import authenticate, login, logout` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 1 lines).** In Step 80, logout function involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.views.decorators.http import require_POST` makes a Python function, class or module available to the code that follows.

**Block 3 (python, 4 lines).** In Step 80, logout function involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** `@require_POST` adds a check or behaviour around the view before Django runs the view itself. `def logoutpage(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `return redirect('login')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 80. For Step 80, the proposed way to check logout function was to revisit `accounts/views.py` and run the corresponding command or feature to see its result. Development continued in Step 81 before a complete result could be claimed.

#### Next

[Step 79: Understand a Django project versus an app](../Module-1/03-login.md#step-79) · [Step 81: Logout URL](#step-81)


---

<a id="step-81"></a>

### Step 81: Logout URL

#### Before this step

The logout function was ready, but the browser did not have an address for calling it. Immediately before this, Step 80 dealt with logout function; the work here continued the same part of the application.

#### What we did and why

Adding the logout URL connects a named address to the function. The address alone is not a button; we build the visible control next.

#### Code and commands

**Step 81, block 1.** This code sample or terminal command was used while working on logout url. The content below remains in its original sequence for Step 81, block 1.

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
]
```

#### How the code works

**Block 1 (python, 4 lines).** In Step 81, logout url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** `urlpatterns = [` sets the named value used by the next operation in this file. `path('login/', views.loginpage, name='login'),` begins a URL rule connecting a browser address with the function that answers it. `path('logout/', views.logoutpage, name='logout'),` begins a URL rule connecting a browser address with the function that answers it.

#### How we checked it

Not tested yet at Step 81. For Step 81, the proposed way to check logout url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Development continued in Step 82 before a complete result could be claimed.

#### Next

[Step 80: Logout Function](#step-80) · [Step 82: Logout Button](#step-82)
