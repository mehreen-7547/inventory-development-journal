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
