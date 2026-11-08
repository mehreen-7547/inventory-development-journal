# Module 1, Chapter 8: Role security (decorators)

## Step 100: Role Security

#### Next: Create the role-permission decorator

Our homepage currently displays different content for Admin, Manager and Sales users. However, **displaying different content is not the same as restricting access**.

We need a reusable permission check.

**Create a new file:** `accounts/decorators.py`

Paste:

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

#### What this will do

Later, we can protect a view like this:

```python
@role_required('admin', 'manager')
def example_view(request):
    ...
```

- Admin and Manager can access it.
- Sales users receive **403 Forbidden**.
- Users who aren't logged in are redirected to the login page.

    **NEW vs CLASS — 2 concepts**

  **1. Custom decorator:** Your instructor used Django's built-in `@login_required`. We're creating a similar reusable check for roles.

  **2. `PermissionDenied`:** Django's built-in exception for rejecting unauthorized access with HTTP 403.

For now, **only create and save this file**. We'll apply and test the decorator next.

*Previous: Step 99 (Commit Changes)  |  Next: Step 101 (Understanding Execution Flow)*

---

## Step 101: Understanding Execution Flow

The missing connection is this: **`def` creates a function, while `return` sends something back to whoever called that function.** And importantly, returning a function is different from executing it.

Look at our code without the extra security details:

```python
def role_required(*roles):
    def decorator(view_func):
        def wrapper(request, *args, **kwargs):
            return view_func(request, *args, **kwargs)

        return wrapper

    return decorator
```

There are **three functions nested inside one another**. Let's follow exactly what Python does.

#### Part 1 — When Django prepares the view

Suppose you write:

```python
@role_required('admin', 'manager')
def inventory_report(request):
    return ...
```

**First**, Python calls `role_required('admin', 'manager')`.

It enters the outer function:

```python
def role_required(*roles):
```

It remembers the allowed roles and encounters:

```python
    def decorator(view_func):
```

Does Python execute `decorator` now? **No.** The `def` statement merely creates that function.

Python continues until:

```python
    return decorator
```

This means: *Give the `decorator` function itself back to whoever called `role_required`.*

Notice that we wrote `return decorator`, **not** `return decorator()`. There are no parentheses, so we're returning the function, not calling it.

**Second**, Python calls that returned `decorator` function, passing the original `inventory_report` function into its `view_func` parameter.

Now Python enters:

```python
def decorator(view_func):
```

Here, `view_func` refers to our original `inventory_report` function.

Inside, Python encounters:

```python
    def wrapper(request, *args, **kwargs):
```

Again, this `def` only creates a function. It does not execute the wrapper.

Then Python reaches:

```python
    return wrapper
```

This returns the `wrapper` function itself.

**What has happened?** Python has now replaced the name `inventory_report` with the wrapper function. But the wrapper still remembers the original `inventory_report` through `view_func`.

#### Part 2 — When someone actually visits the page

Now imagine a Manager opens `/inventory-report/`.

Django calls what it knows as `inventory_report`. But remember, that name now refers to the wrapper.

So Python finally executes:

```python
def wrapper(request, *args, **kwargs):
```

Here, `request` is the actual HTTP request. `*args` and `**kwargs` carry any additional arguments the original view might need.

Our full wrapper checks whether the user is logged in and whether their role is allowed.

If the Manager passes those checks, Python reaches:

```python
return view_func(request, *args, **kwargs)
```

**This return is different.** Notice the parentheses after `view_func`.

We're now **calling the original inventory report function**, passing it the request and any additional arguments. That original function produces a response, and `return` sends that response back through the wrapper to Django.

#### The complete flow

  ONCE, WHEN THE VIEW IS PREPARED

    `role_required('admin', 'manager')`

    `return decorator`

    `decorator(inventory_report)`

    `return wrapper`

  EVERY TIME A VISITOR REQUESTS THE PAGE

    `wrapper(request, ...)`

    Check login and role

    `return view_func(request, ...)`

    Original view runs and its response is returned

The essential distinction is:

- `return decorator` — hands back a function to be used in the next preparation stage.
- `return wrapper` — hands back the function that will handle future requests.
- `return view_func(...)` — actually executes the original view and returns its result.

**So the first two returns prepare the protection. The third return happens when an authorized visitor actually requests the page.**

That is why we need the three nested functions: one receives the allowed roles, one receives the view to protect, and one receives the actual request.

*Previous: Step 100 (Role Security)  |  Next: Step 102 (Complete Execution Flow)*

---
