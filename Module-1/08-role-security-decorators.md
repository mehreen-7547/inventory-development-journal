# MODULE 1 — Accounts

## Chapter 8: Role security (decorators) (Steps 100–107)

**Where we are.** The application had multiple roles, but a visible role label is not an access-control rule. We added reusable server-side checks.

**What this chapter gives us.** Understand decorators, nested functions, PermissionDenied, and 403 testing.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Trace what runs during decoration and what runs when a request arrives.

### Find a step

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

**Why we are doing this.** Different home content was not enough: a user could still type a URL for a page their role should not access.

**File / location.** `accounts/decorators.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

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

*2. Recorded snippet (illustrative or partial; not a complete paste-ready file).*

```python
@role_required('admin', 'manager')
def example_view(request):
    ...
```

**How it works.** `role_required(*roles)` is a reusable decorator that checks both authentication and allowed role values before executing a view. Denied access is handled on the server.

**Follow the flow.** When you write `@role_required('admin')`, Python calls `role_required` with the allowed role and gets a decorator back. That decorator wraps the view. On a request, `login_required` first verifies sign-in. The wrapper then checks `request.user.role`. If the role is missing from the allowed set, it raises `PermissionDenied` and Django sends HTTP 403. Otherwise the original view runs normally.

**How we checked it.** Its enforcement and temporary restriction tests appear in Steps 103–107.

**Connects to.** Previous: [Step 99 — Commit Changes](07-role-based-homepage-and-feedback-messages.md#step-99) · Next: [Step 101 — Understand nested decorator functions](08-role-security-decorators.md#step-101)


---

<a id="step-101"></a>

### Step 101: Understand nested decorator functions

**Why we are doing this.** The decorator introduced in Step 100 has nested functions, which can be confusing if `def`, a function call, and a returned function are treated as the same thing.

**File / location.** `accounts/decorators.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Look at our code without the extra security details.*

```python
def role_required(*roles):
    def decorator(view_func):
        def wrapper(request, *args, **kwargs):
            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

**How it works.** `role_required('admin')` first creates a decorator; Django applies that decorator to a view; the returned wrapper runs for each browser request. The wrapper decides whether to call the original view.

**How we checked it.** This is an explanation of the decorator's execution order, not another new permission rule.

**Connects to.** Previous: [Step 100 — Role Security](08-role-security-decorators.md#step-100) · Next: [Step 102 — Trace the decorator execution order](08-role-security-decorators.md#step-102)


---

<a id="step-102"></a>

### Step 102: Trace the decorator execution order

**Why we are doing this.** We revisited the decorator with a complete trace so it could be explained in an interview without memorizing syntax.

**File / location.** `accounts/decorators.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```python
@login_required(login_url='/login/')
@wraps(view_func)
def wrapper(request, *args, **kwargs):
    if request.user.role not in roles:
        raise PermissionDenied

    return view_func(request, *args, **kwargs)
```

**How it works.** Decoration happens when Django imports the view; permission checks happen later when a request arrives. The wrapper calls the original view only for an authorized user.

The login-required decorator prevents an anonymous request from reaching the protected view.

**How we checked it.** The code snippets demonstrate the flow. No separate implementation change was made.

**Connects to.** Previous: [Step 101 — Understand nested decorator functions](08-role-security-decorators.md#step-101) · Next: [Step 103 — Apply Role Security](08-role-security-decorators.md#step-103)


---

<a id="step-103"></a>

### Step 103: Apply Role Security

**Why we are doing this.** The reusable decorator was written, but it provided no protection until applied to a real view.

**File / location.** `core/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace its existing code with.*

```python
from django.shortcuts import render
from accounts.decorators import role_required


@role_required('admin', 'manager', 'sales')
def home(request):
    return render(request, 'core/home.html')
```

*2. Recorded snippet.*

```python
@login_required(login_url='/login/')
```

*3. Recorded snippet; Now we use.*

```python
@role_required('admin', 'manager', 'sales')
```

**How it works.** Importing `role_required` in `core/views.py` and decorating `home` makes Django run the role check before rendering the dashboard.

**Follow the flow.** The decorator in `accounts/decorators.py` is reusable, but writing it does not protect any page by itself. Importing it into `core/views.py` and putting `@role_required(...)` above `home` connects the rule to real incoming requests. The allowed list determines which of the business roles can see that page.

**How we checked it.** All three roles remained allowed initially; the next step temporarily removes one role to test denial.

**Connects to.** Previous: [Step 102 — Trace the decorator execution order](08-role-security-decorators.md#step-102) · Next: [Step 104 — Test Role Restrictions](08-role-security-decorators.md#step-104)


---

<a id="step-104"></a>

### Step 104: Test Role Restrictions

**Why we are doing this.** To verify real authorization, we needed a user who should be refused, rather than only testing allowed cases.

**File / location.** `core/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In core/views.py, change.*

```python
@role_required('admin', 'manager', 'sales')
```

*2. Recorded snippet.*

```python
@role_required('admin', 'manager')
```

**How it works.** Temporarily excluding `sales` from the decorator should make a sales account receive HTTP 403. The allowed-role list should be restored after the test.

**Follow the flow.** The temporary test deliberately changes the rule to allow only Admin and Manager. Log in as a Sales account: it is signed in, yet its role is not allowed, so it should receive 403. Afterwards restore Sales in the allowed list. The important lesson is that authentication and authorization are two separate questions.

**How we checked it.** The source gives the expected 403 test procedure. This temporary restriction is not the final design.

**Connects to.** Previous: [Step 103 — Apply Role Security](08-role-security-decorators.md#step-103) · Next: [Step 105 — Review the role decorator](08-role-security-decorators.md#step-105)


---

<a id="step-105"></a>

### Step 105: Review the role decorator

**Why we are doing this.** We checked the complete `accounts/decorators.py` definition against the intended permission flow.

**File / location.** `accounts/decorators.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; This was the step where we created accounts/decorators.py to restrict access based on user roles.*

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

**How it works.** `functools.wraps` preserves the original view's metadata; `login_required` handles anonymous users, and `PermissionDenied` provides an HTTP 403 for signed-in users with disallowed roles.

The login-required decorator prevents an anonymous request from reaching the protected view.

**How we checked it.** This is partly a review/repetition of Step 100, not a new second decorator.

**Connects to.** Previous: [Step 104 — Test Role Restrictions](08-role-security-decorators.md#step-104) · Next: [Step 106 — Apply Role Security](08-role-security-decorators.md#step-106)


---

<a id="step-106"></a>

### Step 106: Apply Role Security

**Why we are doing this.** The earlier homepage code still used login-only protection in one working version. We replaced it with the stronger role-aware decorator.

**File / location.** `core/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Now replace the entire file with.*

```python
from django.shortcuts import render
from accounts.decorators import role_required


@role_required('admin', 'manager', 'sales')
def home(request):
    return render(request, 'core/home.html')
```

**How it works.** The `home` view imports `role_required` from the accounts app and explicitly lists roles permitted on the dashboard. That security decision is enforced on every request.

The decorator checks the signed-in user’s role before the view runs; a hidden link alone would not enforce permission.

**How we checked it.** The following step supplies a concrete denial test using a sales account.

**Connects to.** Previous: [Step 105 — Review the role decorator](08-role-security-decorators.md#step-105) · Next: [Step 107 — Test Role Security](08-role-security-decorators.md#step-107)


---

<a id="step-107"></a>

### Step 107: Test Role Security

**Why we are doing this.** We performed a focused permission test to distinguish signed-in-but-forbidden from not-signed-in.

**File / location.** `core/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In core/views.py, temporarily change.*

```python
@role_required('admin', 'manager', 'sales')
```

*2. Recorded snippet.*

```python
@role_required('admin', 'manager')
```

**How it works.** Temporarily limiting the home view to admin and manager should block sales with 403; restoring sales afterwards returns the intended access rules.

The decorator checks the signed-in user’s role before the view runs; a hidden link alone would not enforce permission.

**How we checked it.** The source describes this manual test and restoration, but does not include an automated test result.

**Connects to.** Previous: [Step 106 — Apply Role Security](08-role-security-decorators.md#step-106) · Next: [Step 108 — Understand REQUIRED_FIELDS](09-email-validation-and-email-setup.md#step-108)

---

**Chapter recap.** Understand decorators, nested functions, PermissionDenied, and 403 testing.

**Practice explaining:** Trace what runs during decoration and what runs when a request arrives.

