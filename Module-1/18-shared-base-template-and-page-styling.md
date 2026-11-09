# MODULE 1 — Employee accounts

## Chapter 18: Shared base template and page styling (Steps 177–194)

### Before we begin

The login and employee pages had grown one by one. They worked, but many contained repeated headers, menus and form styling.

### What we worked on

We built a shared page layout, updated each account screen to use it and corrected where form styling belonged. At the end, we also handled a case where an employee account saves successfully but its welcome email fails.

### Picture it this way

The shared base.html template is a page frame reused everywhere. Each child page fills in only its own content. The last two steps are about making the app explain email failures accurately, not about changing the shared layout.

### Find a step

- [Step 177: Plan the shared layout](#step-177)

---

<a id="step-177"></a>

### Step 177: Plan the shared layout

#### Before this step

We had reached the part of the employee accounts module dealing with plan the shared layout. This step followed the work immediately before it. The preceding step, Step 176, covered send a real email through gmail, so this was the next part of the development sequence.

#### What we did and why

We worked through plan the shared layout using the project files and commands shown below.

#### Code and commands

**Step 177, block 1.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 1.

```python
from django.core.exceptions import PermissionDenied
```

**Step 177, block 2.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 2.

```python
from django.contrib import messages
from django.shortcuts import redirect
```

**Step 177, block 3.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 3.

```python
if request.user.role not in roles:
    raise PermissionDenied
```

**Step 177, block 4.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 4.

```python
if request.user.role not in roles:
    messages.error(request, 'You do not have permission to access this page.')
    return redirect('home')
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.core.exceptions import PermissionDenied` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 2 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from django.contrib import messages` makes a Python function, class or module available to the code that follows. `from django.shortcuts import redirect` makes a Python function, class or module available to the code that follows.

**Block 3 (python, 2 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied.

**Block 4 (python, 3 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied. `messages.error(request, 'You do not have permission to access this page.')` prepares feedback that can appear when the user reaches the next page. `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 177. For Step 177, the proposed way to check plan the shared layout was to revisit `base.html` and run the corresponding command or feature to see its result. Development continued in Step 178 before a complete result could be claimed.

#### Next

[Step 176: Send a real email through Gmail](../Module-1/17-real-email-delivery-smtp.md#step-176) · [Step 178: Create the shared base.html template](#step-178)
