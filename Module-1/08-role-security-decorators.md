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
