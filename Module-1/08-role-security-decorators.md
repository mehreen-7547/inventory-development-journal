# MODULE 1 — Employee accounts

## Chapter 8: Role security (decorators) (Steps 100–107)

### Before we begin

We had started showing different links to different roles. That made the page nicer, but it did not make an address secure.

### What we worked on

We wrote a role-checking decorator, attached it to selected views and tested what happens when a person with the wrong role tries to open a page.

### Picture it this way

A decorator is a guard placed in front of a Django view. It checks who is asking and which role they have before allowing the real page function to run.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 100: Role Security](#step-100)
- [Step 101: Understand nested decorator functions](#step-101)
- [Step 102: Trace the decorator execution order](#step-102)
- [Step 103: Apply Role Security](#step-103)
- [Step 104: Test Role Restrictions](#step-104)
- [Step 105: Review the role decorator](#step-105)
- [Step 106: Apply Role Security](#step-106)
- [Step 107: Test Role Security](#step-107)

---

<a id="step-100"></a>

### Step 100: Role Security

**What was happening before this step?**

We could hide links by role, but someone could still type a restricted address directly. We needed real protection in the Django views.

**What did we do, and what does it mean?**

A decorator is a small function wrapped around another function. Our role decorator checks the current user's role before allowing a protected view to do its normal work.

**File or place:** `accounts/decorators.py`.

**Code or command from this step**

*1. Paste.*

```python
from functools import wraps
from django.contrib.auth.decorators import login_required
from django.core.exceptions import PermissionDenied


def role_required(*roles):
    def decorator(view_func):
        @login_required(login_url='/login/')
        @wraps(view_func)
        def wrapper(request, *args, **kwargs):
            if request.user.role not in roles:
                raise PermissionDenied

            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

*2. Code or command used at this point. This is a shortened illustration, not a complete file.*

```python
@role_required('admin', 'manager')
def example_view(request):
    ...
```

**Understanding the code, one piece at a time**

A decorator can be read as “before running this view, do one extra check.” In this project, the extra check is whether the current user has the required business role. Only accepted requests reach the original view function.

**How did we know what happened?**

Its enforcement and temporary restriction tests appear in Steps 103–107.

**Where to go next:** Previous: [Step 99 — Commit Changes](07-role-based-homepage-and-feedback-messages.md#step-99) · Next: [Step 101 — Understand nested decorator functions](08-role-security-decorators.md#step-101)


---

<a id="step-101"></a>

### Step 101: Understand nested decorator functions

**What was happening before this step?**

The decorator had several nested functions, so the order of execution was difficult to picture.

**What did we do, and what does it mean?**

Think of a security guard standing before a room. The outside function sets up which roles may enter. When someone requests the page, the guard checks the current user before letting the actual view run.

**File or place:** `accounts/decorators.py`.

**Code or command from this step**

*1. Look at our code without the extra security details.*

```python
def role_required(*roles):
    def decorator(view_func):
        def wrapper(request, *args, **kwargs):
            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

**How did we know what happened?**

This is an explanation of the decorator's execution order, not another new permission rule.

**Where to go next:** Previous: [Step 100 — Role Security](08-role-security-decorators.md#step-100) · Next: [Step 102 — Trace the decorator execution order](08-role-security-decorators.md#step-102)


---

<a id="step-102"></a>

### Step 102: Trace the decorator execution order

**What was happening before this step?**

We followed one protected request through the full decorator to understand each return.

**What did we do, and what does it mean?**

The browser asks for a page, the decorator checks login and allowed roles, and only then calls the original view. If the user is not allowed, Django returns a refusal instead of showing the protected information.

**File or place:** `accounts/decorators.py`.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```python
@login_required(login_url='/login/')
@wraps(view_func)
def wrapper(request, *args, **kwargs):
    if request.user.role not in roles:
        raise PermissionDenied

    return view_func(request, *args, **kwargs)
```

**Understanding the code, one piece at a time**

There are three possible situations to picture: an unsigned visitor needs to log in; a signed-in person with the wrong role is refused; a signed-in person with an allowed role reaches the page. A secure view needs to handle all three.

**How did we know what happened?**

The code snippets demonstrate the flow. No separate implementation change was made.

**Where to go next:** Previous: [Step 101 — Understand nested decorator functions](08-role-security-decorators.md#step-101) · Next: [Step 103 — Apply Role Security](08-role-security-decorators.md#step-103)


---

<a id="step-103"></a>

### Step 103: Apply Role Security

**What was happening before this step?**

The role-checking function was written. Now a page actually had to use it.

**What did we do, and what does it mean?**

Placing `@role_required(...)` above a view attaches that check to requests for this page. Without applying it, writing the decorator file alone would not protect anything.

**File or place:** `core/views.py`.

**Code or command from this step**

*1. Replace its existing code with.*

```python
from django.shortcuts import render
from accounts.decorators import role_required


@role_required('admin', 'manager', 'sales')
def home(request):
    return render(request, 'core/home.html')
```

*2. Code or command used at this point.*

```python
@login_required(login_url='/login/')
```

*3. Now we use.*

```python
@role_required('admin', 'manager', 'sales')
```

**Understanding the code, one piece at a time**

`render(...)` combines a page template with data from the view and returns the result to the browser.

**How did we know what happened?**

All three roles remained allowed initially; the next step temporarily removes one role to test denial.

**Where to go next:** Previous: [Step 102 — Trace the decorator execution order](08-role-security-decorators.md#step-102) · Next: [Step 104 — Test Role Restrictions](08-role-security-decorators.md#step-104)


---

<a id="step-104"></a>

### Step 104: Test Role Restrictions

**What was happening before this step?**

Security needs to be tested with a person who should be refused, not only someone who should succeed.

**What did we do, and what does it mean?**

We tried or planned the denied-role case. A signed-in person with the wrong role should be stopped even though their username and password are valid.

**File or place:** `core/views.py`.

**Code or command from this step**

*1. In core/views.py, change.*

```python
@role_required('admin', 'manager', 'sales')
```

*2. Code or command used at this point.*

```python
@role_required('admin', 'manager')
```

**How did we know what happened?**

Our notes gives the expected 403 test procedure. This temporary restriction is not the final design.

**Where to go next:** Previous: [Step 103 — Apply Role Security](08-role-security-decorators.md#step-103) · Next: [Step 105 — Review the role decorator](08-role-security-decorators.md#step-105)


---

<a id="step-105"></a>

### Step 105: Review the role decorator

**What was happening before this step?**

We reviewed the finished decorator code after testing and corrections.

**What did we do, and what does it mean?**

The important parts are still the same: identify the current user, compare their role with the allowed roles, and either run the view or return a denial. This step consolidated the earlier explanation rather than inventing a new security system.

**File or place:** `accounts/decorators.py`.

**Code or command from this step**

*1. This was the step where we created accounts/decorators.py to restrict access based on user roles.*

```python
from functools import wraps
from django.contrib.auth.decorators import login_required
from django.core.exceptions import PermissionDenied


def role_required(*roles):
    def decorator(view_func):
        @login_required(login_url='/login/')
        @wraps(view_func)
        def wrapper(request, *args, **kwargs):
            if request.user.role not in roles:
                raise PermissionDenied

            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

**Understanding the code, one piece at a time**

`@login_required` is written above the view so Django checks whether the visitor has signed in before showing that page.

**How did we know what happened?**

This is partly a review/repetition of Step 100, not a new second decorator.

**Where to go next:** Previous: [Step 104 — Test Role Restrictions](08-role-security-decorators.md#step-104) · Next: [Step 106 — Apply Role Security](08-role-security-decorators.md#step-106)


---

<a id="step-106"></a>

### Step 106: Apply Role Security

**What was happening before this step?**

One working homepage version still checked only whether a person was signed in. That was too broad for the latest rules.

**What did we do, and what does it mean?**

We replaced the login-only check with the role-aware decorator where required. Being signed in answers who you are; role permission answers whether you may use this page.

**File or place:** `core/views.py`.

**Code or command from this step**

*1. Now replace the entire file with.*

```python
from django.shortcuts import render
from accounts.decorators import role_required


@role_required('admin', 'manager', 'sales')
def home(request):
    return render(request, 'core/home.html')
```

**Understanding the code, one piece at a time**

`render(...)` combines a page template with data from the view and returns the result to the browser.

**How did we know what happened?**

The following step supplies a concrete denial test using a sales account.

**Where to go next:** Previous: [Step 105 — Review the role decorator](08-role-security-decorators.md#step-105) · Next: [Step 107 — Test Role Security](08-role-security-decorators.md#step-107)


---

<a id="step-107"></a>

### Step 107: Test Role Security

**What was happening before this step?**

With the decorator attached, we checked a case where the person was signed in but did not have the right permission.

**What did we do, and what does it mean?**

A “forbidden” result tells us the server blocked the request. The important test is what the server returns, not whether a button happens to be hidden.

**File or place:** `core/views.py`.

**Code or command from this step**

*1. In core/views.py, temporarily change.*

```python
@role_required('admin', 'manager', 'sales')
```

*2. Code or command used at this point.*

```python
@role_required('admin', 'manager')
```

**How did we know what happened?**

Our notes describes this manual test and restoration, but does not include an automated test result.

**Where to go next:** Previous: [Step 106 — Apply Role Security](08-role-security-decorators.md#step-106) · Next: [Step 108 — Understand REQUIRED_FIELDS](09-email-validation-and-email-setup.md#step-108)

---

### The point of this chapter

A decorator is a guard placed in front of a Django view. It checks who is asking and which role they have before allowing the real page function to run.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

