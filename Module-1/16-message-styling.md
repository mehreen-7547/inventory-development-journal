# MODULE 1 — Employee accounts

## Chapter 16: Message styling (Steps 165–169)

### Before we begin

The account-management views were saving changes and setting messages, but those messages were not always easy to see.

### What we worked on

We gave the employee screens clear places to show success and error messages, then added styling so the notices would stand out.

### Picture it this way

When a view saves a change and redirects back to a list, Django can carry a short message to the next page. The destination page must display that message, otherwise the user does not know what happened.

### Find a step

- [Step 165: Employee Management Messages](#step-165)

---

<a id="step-165"></a>

### Step 165: Employee Management Messages

#### Before this step

The create and edit views could set success or error messages, but a message is useless if the next page never shows it. The preceding step, Step 164, covered home template review, so this was the next part of the development sequence.

#### What we did and why

We added message display to the employee-management page. Django carries a short notice across the redirect, and the template prints it for the administrator.

#### Code and commands

**Step 165, block 1.** This code sample or terminal command was used while working on employee management messages. The content below remains in its original sequence for Step 165, block 1.

```html
<h2>Manage Employees</h2>
```

**Step 165, block 2.** This code sample or terminal command was used while working on employee management messages. The content below remains in its original sequence for Step 165, block 2.

```html
{% if messages %}

    {% for message in messages %}

        <p>{{ message }}</p>

    {% endfor %}

{% endif %}
```

#### How the code works

**Block 1 (html, 1 lines).** In Step 165, employee management messages involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<h2>Manage Employees</h2>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 9 lines).** In Step 165, employee management messages involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if messages %}` decides which HTML is included based on the value Django provided. `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p>{{ message }}</p>` creates or closes part of the page structure seen by the browser.

**Lines 8–9:** The lines starting with `{% endif %}` supply the values or page content for this part of templates/accounts/employee_list.html. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 165. For Step 165, the proposed way to check employee management messages was to revisit `templates/accounts/employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 166 before a complete result could be claimed.

#### Next

[Step 164: Home Template Review](../Module-1/15-activate-deactivate-and-resend-setup-email.md#step-164) · [Step 166: Display edit-page feedback messages](#step-166)
