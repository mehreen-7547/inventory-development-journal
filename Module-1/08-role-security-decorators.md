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

## Step 102: Complete Execution Flow

You're very close, but there is **one important mistake** in your understanding: the three functions do not execute one after another and pass their results back up the chain.

Instead, **the first two functions prepare the third function. The third function executes later, when someone visits the page.**

Let's connect everything, including who passes the arguments.

#### First, look at where we use the decorator

```python
@role_required('admin', 'manager')
def inventory_report(request):
    return render(request, 'inventory/report.html')
```

When Python encounters this code, it does something equivalent to:

```python
original_view = inventory_report

step1 = role_required('admin', 'manager')

step2 = step1(original_view)

inventory_report = step2
```

This is the connection you were missing.

**First:** Python calls `role_required('admin', 'manager')`. That function creates `decorator` and executes `return decorator`. So `step1` now refers to the `decorator` function.

**Second:** Python calls `step1(original_view)`. Since `step1` is actually `decorator`, Python is now calling:

```python
decorator(original_view)
```

That's how `decorator` receives `view_func`. **Python automatically passes the original view function to the decorator because we used the `@` syntax.** It receives the actual function object, not merely its name.

Inside `decorator`, Python creates `wrapper`, applies the two decorators above it, and executes `return wrapper`.

**Third:** The returned, protected wrapper is assigned to the name `inventory_report`.

At this point, the preparation is finished. Neither `role_required` nor `decorator` needs to execute again for each visitor.

#### Now, what about `@login_required` and `@wraps`?

Look at this portion:

```python
@login_required(login_url='/login/')
@wraps(view_func)
def wrapper(request, *args, **kwargs):
    if request.user.role not in roles:
        raise PermissionDenied

    return view_func(request, *args, **kwargs)
```

These are also decorators! They work on the same principle.

- **`@wraps(view_func)`** preserves information about the original view, such as its name, on `wrapper`.
- **`@login_required(...)`** adds another protective layer around `wrapper`. It checks whether the visitor is logged in before allowing the role check to execute.

Python applies `@wraps` first, then `@login_required`.

So our final protected view has two security checks: login first, then role.

#### Finally, who gives `wrapper` its `request`?

**Django does.**

Imagine a Manager opens `/inventory-report/` in the browser.

Django receives an HTTP request, checks `urls.py`, finds the view assigned to that URL, and calls it with the request object.

Remember that `inventory_report` now refers to the protected wrapper.

The execution looks like this:

    **Browser requests `/inventory-report/`**

    **Django calls the protected view with `request`**

    **`login_required` checks login**

    **`wrapper` checks the user's role**

    **`view_func(request, ...)` executes the original view**

    **HTTP response returns to Django**

The `*args` and `**kwargs` are additional positional and keyword arguments that Django may pass from the URL. For example, a URL containing a product ID might supply `product_id=5`. The wrapper accepts those arguments and passes them unchanged to the original view.

#### Now, let's correct your original explanation

You said:

> Wrapper gives its result back to decorator, decorator gives its result back to role_required, and role_required gives its result back to whoever called it.

**That part is incorrect.**

The actual sequence is:

**During preparation:**

`role_required()` returns the `decorator` function → `decorator()` returns the protected `wrapper` function → Python assigns that function to the view's name.

**During an HTTP request:**

Django calls the protected function → `wrapper` calls the original view → the original view returns its response → `wrapper` returns that response through the login-check layer to Django.

The outer functions have already finished. The wrapper doesn't return its response to them.

**The key distinction:** Nesting functions in the code does not mean their execution and return values follow the same nesting. Each `return` goes back to the caller of that particular function.

*Previous: Step 101 (Understanding Execution Flow)  |  Next: Step 103 (Apply Role Security)*

---
