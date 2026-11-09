# MODULE 1 — Employee accounts

## Chapter 7: Role-based homepage and feedback messages (Steps 91–99)

### Before we begin

The homepage and feedback messages were working, but people with different jobs were seeing similar information.

### What we worked on

We made the homepage look different by role and showed useful login/password messages. We then saved the code changes in Git.

### Picture it this way

This chapter is about what the user sees. Proper server-side protection for restricted pages is a different matter, and we build it in the next chapter.

### Find a step

- [Step 91: Role-Based Homepage](#step-91)

---

<a id="step-91"></a>

### Step 91: Role-Based Homepage

#### Before this step

The same homepage was being shown to people with different jobs. We wanted the text and options to reflect their role. The preceding step, Step 90, covered change password link, so this was the next part of the development sequence.

#### What we did and why

The template checks the signed-in user's role and shows suitable labels or sections. For example, an administrator should see account-management options that sales staff do not need.

#### Code and commands

**Step 91, block 1.** This code sample or terminal command was used while working on role-based homepage. The content below remains in its original sequence for Step 91, block 1.

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

#### How the code works

**Block 1 (html, 9 lines).** In Step 91, role-based homepage involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if request.user.role == 'admin' %}` decides which HTML is included based on the value Django provided. The HTML element `<p>Administrator Dashboard</p>` creates or closes part of the page structure seen by the browser. `{% elif request.user.role == 'manager' %}` decides which HTML is included based on the value Django provided. The HTML element `<p>Inventory Manager Dashboard</p>` creates or closes part of the page structure seen by the browser.

**Lines 8–9:** The HTML element `<p>Sales Staff Dashboard</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 91. For Step 91, the proposed way to check role-based homepage was to open the related page in the browser and check that the route reached the intended view or template in `core/templates/core/home.html`. Development continued in Step 92 before a complete result could be claimed.

#### Next

[Step 90: Change Password Link](../Module-1/06-password-change.md#step-90) · [Step 92: Admin Link Visibility](#step-92)
