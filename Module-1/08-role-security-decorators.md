# MODULE 1 — Employee accounts

## Chapter 8: Role security (decorators) (Steps 100–107)

### Before we begin

We had started showing different links to different roles. That made the page nicer, but it did not make an address secure.

### What we worked on

We wrote a role-checking decorator, attached it to selected views and tested what happens when a person with the wrong role tries to open a page.

### Picture it this way

A decorator is a guard placed in front of a Django view. It checks who is asking and which role they have before allowing the real page function to run.

### Find a step

- [Step 100: Role Security](#step-100)
- [Step 101: Understand nested decorator functions](#step-101)
- [Step 102: Trace the decorator execution order](#step-102)
- [Step 103: Apply Role Security](#step-103)
- [Step 104: Test Role Restrictions](#step-104)
- [Step 105: Review the role decorator](#step-105)

---

<a id="step-100"></a>

### Step 100: Role Security

#### Before this step

We could hide links by role, but someone could still type a restricted address directly. We needed real protection in the Django views. The preceding step, Step 99, covered commit changes, so this was the next part of the development sequence.

#### What we did and why

A decorator is a small function wrapped around another function. Our role decorator checks the current user's role before allowing a protected view to do its normal work.

#### Code and commands

**Step 100, block 1.** This code sample or terminal command was used while working on role security. The content below remains in its original sequence for Step 100, block 1.

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

**Step 100, block 2.** This code sample or terminal command was used while working on role security. The content below remains in its original sequence for Step 100, block 2.

```python
@role_required('admin', 'manager')
def example_view(request):
    ...
```

#### How the code works

**Block 1 (python, 18 lines).** In Step 100, role security involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from functools import wraps` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.decorators import login_required` makes a Python function, class or module available to the code that follows. `from django.core.exceptions import PermissionDenied` makes a Python function, class or module available to the code that follows. `def role_required(*roles):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Lines 8–14:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `@wraps(view_func)` adds a check or behaviour around the view before Django runs the view itself. `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied.

**Lines 15–18:** `return wrapper` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. `return decorator` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 2 (python, 3 lines).** In Step 100, role security involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `@role_required('admin', 'manager')` adds a check or behaviour around the view before Django runs the view itself. `def example_view(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

#### How we checked it

Not tested yet at Step 100. For Step 100, the proposed way to check role security was to revisit `accounts/decorators.py` and run the corresponding command or feature to see its result. Development continued in Step 101 before a complete result could be claimed.

#### Next

[Step 99: Commit Changes](../Module-1/07-role-based-homepage-and-feedback-messages.md#step-99) · [Step 101: Understand nested decorator functions](#step-101)


---

<a id="step-101"></a>

### Step 101: Understand nested decorator functions

#### Before this step

The decorator had several nested functions, so the order of execution was difficult to picture. Immediately before this, Step 100 dealt with role security; the work here continued the same part of the application.

#### What we did and why

Think of a security guard standing before a room. The outside function sets up which roles may enter. When someone requests the page, the guard checks the current user before letting the actual view run.

#### Code and commands

**Step 101, block 1.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 1.

```python
def role_required(*roles):
    def decorator(view_func):
        def wrapper(request, *args, **kwargs):
            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

**Step 101, block 2.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 2.

```python
@role_required('admin', 'manager')
def inventory_report(request):
    return ...
```

**Step 101, block 3.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 3.

```python
def role_required(*roles):
```

**Step 101, block 4.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 4.

```python
    def decorator(view_func):
```

**Step 101, block 5.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 5.

```python
    return decorator
```

**Step 101, block 6.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 6.

```python
def decorator(view_func):
```

**Step 101, block 7.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 7.

```python
    def wrapper(request, *args, **kwargs):
```

**Step 101, block 8.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 8.

```python
    return wrapper
```

**Step 101, block 9.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 9.

```python
def wrapper(request, *args, **kwargs):
```

**Step 101, block 10.** This code sample or terminal command was used while working on understand nested decorator functions. The content below remains in its original sequence for Step 101, block 10.

```python
return view_func(request, *args, **kwargs)
```

#### How the code works

**Block 1 (python, 8 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `def role_required(*roles):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `def decorator(view_func):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `return view_func(request, *args, **kwargs)` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Lines 8–8:** `return decorator` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 2 (python, 3 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `@role_required('admin', 'manager')` adds a check or behaviour around the view before Django runs the view itself. `def inventory_report(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `return ...` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 3 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `def role_required(*roles):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 4 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `def decorator(view_func):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 5 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return decorator` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 6 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `def decorator(view_func):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 7 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 8 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return wrapper` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 9 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 10 (python, 1 lines).** In Step 101, understand nested decorator functions involved `the Django project files`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `return view_func(request, *args, **kwargs)` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 101. For Step 101, the proposed way to check understand nested decorator functions was to revisit `the Django project files` and run the corresponding command or feature to see its result. Development continued in Step 102 before a complete result could be claimed.

#### Next

[Step 100: Role Security](#step-100) · [Step 102: Trace the decorator execution order](#step-102)


---

<a id="step-102"></a>

### Step 102: Trace the decorator execution order

#### Before this step

We followed one protected request through the full decorator to understand each return. Immediately before this, Step 101 dealt with understand nested decorator functions; the work here continued the same part of the application.

#### What we did and why

The browser asks for a page, the decorator checks login and allowed roles, and only then calls the original view. If the user is not allowed, Django returns a refusal instead of showing the protected information.

#### Code and commands

**Step 102, block 1.** This code sample or terminal command was used while working on trace the decorator execution order. The content below remains in its original sequence for Step 102, block 1.

```python
@role_required('admin', 'manager')
def inventory_report(request):
    return render(request, 'inventory/report.html')
```

**Step 102, block 2.** This code sample or terminal command was used while working on trace the decorator execution order. The content below remains in its original sequence for Step 102, block 2.

```python
original_view = inventory_report

step1 = role_required('admin', 'manager')

step2 = step1(original_view)

inventory_report = step2
```

**Step 102, block 3.** This code sample or terminal command was used while working on trace the decorator execution order. The content below remains in its original sequence for Step 102, block 3.

```python
decorator(original_view)
```

**Step 102, block 4.** This code sample or terminal command was used while working on trace the decorator execution order. The content below remains in its original sequence for Step 102, block 4.

```python
@login_required(login_url='/login/')
@wraps(view_func)
def wrapper(request, *args, **kwargs):
    if request.user.role not in roles:
        raise PermissionDenied

    return view_func(request, *args, **kwargs)
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 102, trace the decorator execution order involved `inventory/report.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `@role_required('admin', 'manager')` adds a check or behaviour around the view before Django runs the view itself. `def inventory_report(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `return render(request, 'inventory/report.html')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 2 (python, 7 lines).** In Step 102, trace the decorator execution order involved `inventory/report.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `original_view = inventory_report` sets the named value used by the next operation in this file. `step1 = role_required('admin', 'manager')` sets the named value used by the next operation in this file. `step2 = step1(original_view)` sets the named value used by the next operation in this file. `inventory_report = step2` sets the named value used by the next operation in this file.

**Block 3 (python, 1 lines).** In Step 102, trace the decorator execution order involved `inventory/report.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `decorator(original_view)` supply the values or page content for this part of inventory/report.html. These lines are kept together because their order and indentation matter.

**Block 4 (python, 7 lines).** In Step 102, trace the decorator execution order involved `inventory/report.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `@wraps(view_func)` adds a check or behaviour around the view before Django runs the view itself. `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied.

#### How we checked it

Not tested yet at Step 102. For Step 102, the proposed way to check trace the decorator execution order was to revisit `inventory/report.html` and run the corresponding command or feature to see its result. Development continued in Step 103 before a complete result could be claimed.

#### Next

[Step 101: Understand nested decorator functions](#step-101) · [Step 103: Apply Role Security](#step-103)


---

<a id="step-103"></a>

### Step 103: Apply Role Security

#### Before this step

The role-checking function was written. Now a page actually had to use it. Immediately before this, Step 102 dealt with trace the decorator execution order; the work here continued the same part of the application.

#### What we did and why

Placing @role_required(...) above a view attaches that check to requests for this page. Without applying it, writing the decorator file alone would not protect anything.

#### Code and commands

**Step 103, block 1.** This code sample or terminal command was used while working on apply role security. The content below remains in its original sequence for Step 103, block 1.

```python
from django.shortcuts import render
from accounts.decorators import role_required


@role_required('admin', 'manager', 'sales')
def home(request):
    return render(request, 'core/home.html')
```

**Step 103, block 2.** This code sample or terminal command was used while working on apply role security. The content below remains in its original sequence for Step 103, block 2.

```python
@login_required(login_url='/login/')
```

**Step 103, block 3.** This code sample or terminal command was used while working on apply role security. The content below remains in its original sequence for Step 103, block 3.

```python
@role_required('admin', 'manager', 'sales')
```

#### How the code works

**Block 1 (python, 7 lines).** In Step 103, apply role security involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.shortcuts import render` makes a Python function, class or module available to the code that follows. `from accounts.decorators import role_required` makes a Python function, class or module available to the code that follows. `@role_required('admin', 'manager', 'sales')` adds a check or behaviour around the view before Django runs the view itself. `def home(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Block 2 (python, 1 lines).** In Step 103, apply role security involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself.

**Block 3 (python, 1 lines).** In Step 103, apply role security involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `@role_required('admin', 'manager', 'sales')` adds a check or behaviour around the view before Django runs the view itself.

#### How we checked it

Not tested yet at Step 103. For Step 103, the proposed way to check apply role security was to revisit `accounts/decorators.py` and run the corresponding command or feature to see its result. Development continued in Step 104 before a complete result could be claimed.

#### Next

[Step 102: Trace the decorator execution order](#step-102) · [Step 104: Test Role Restrictions](#step-104)


---

<a id="step-104"></a>

### Step 104: Test Role Restrictions

#### Before this step

Security needs to be tested with a person who should be refused, not only someone who should succeed. Immediately before this, Step 103 dealt with apply role security; the work here continued the same part of the application.

#### What we did and why

We tried or planned the denied-role case. A signed-in person with the wrong role should be stopped even though their username and password are valid.

#### Code and commands

**Step 104, block 1.** This code sample or terminal command was used while working on test role restrictions. The content below remains in its original sequence for Step 104, block 1.

```python
@role_required('admin', 'manager', 'sales')
```

**Step 104, block 2.** This code sample or terminal command was used while working on test role restrictions. The content below remains in its original sequence for Step 104, block 2.

```python
@role_required('admin', 'manager')
```

**Step 104, block 3.** This code sample or terminal command was used while working on test role restrictions. The content below remains in its original sequence for Step 104, block 3.

```python
@role_required('admin', 'manager', 'sales')
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 104, test role restrictions involved `core/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `@role_required('admin', 'manager', 'sales')` adds a check or behaviour around the view before Django runs the view itself.

**Block 2 (python, 1 lines).** In Step 104, test role restrictions involved `core/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `@role_required('admin', 'manager')` adds a check or behaviour around the view before Django runs the view itself.

**Block 3 (python, 1 lines).** In Step 104, test role restrictions involved `core/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `@role_required('admin', 'manager', 'sales')` adds a check or behaviour around the view before Django runs the view itself.

#### How we checked it

Not tested yet at Step 104. For Step 104, the proposed way to check test role restrictions was to revisit `core/views.py` and run the corresponding command or feature to see its result. Development continued in Step 105 before a complete result could be claimed.

#### Next

[Step 103: Apply Role Security](#step-103) · [Step 105: Review the role decorator](#step-105)


---

<a id="step-105"></a>

### Step 105: Review the role decorator

#### Before this step

We reviewed the finished decorator code after testing and corrections. Immediately before this, Step 104 dealt with test role restrictions; the work here continued the same part of the application.

#### What we did and why

The important parts are still the same: identify the current user, compare their role with the allowed roles, and either run the view or return a denial. This step consolidated the earlier explanation rather than inventing a new security system.

#### Code and commands

**Step 105, block 1.** This code sample or terminal command was used while working on review the role decorator. The content below remains in its original sequence for Step 105, block 1.

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

**Step 105, block 2.** This code sample or terminal command was used while working on review the role decorator. The content below remains in its original sequence for Step 105, block 2.

```python
@role_required('admin', 'manager')
def inventory_report(request):
    ...
```

#### How the code works

**Block 1 (python, 18 lines).** In Step 105, review the role decorator involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from functools import wraps` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.decorators import login_required` makes a Python function, class or module available to the code that follows. `from django.core.exceptions import PermissionDenied` makes a Python function, class or module available to the code that follows. `def role_required(*roles):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Lines 8–14:** `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `@wraps(view_func)` adds a check or behaviour around the view before Django runs the view itself. `def wrapper(request, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied.

**Lines 15–18:** `return wrapper` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. `return decorator` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 2 (python, 3 lines).** In Step 105, review the role decorator involved `accounts/decorators.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `@role_required('admin', 'manager')` adds a check or behaviour around the view before Django runs the view itself. `def inventory_report(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

#### How we checked it

Not tested yet at Step 105. For Step 105, the proposed way to check review the role decorator was to open the related page in the browser and check that the route reached the intended view or template in `accounts/decorators.py`. Development continued in Step 106 before a complete result could be claimed.

#### Next

[Step 104: Test Role Restrictions](#step-104) · [Step 106: Apply Role Security](#step-106)
