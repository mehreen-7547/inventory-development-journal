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
- [Step 166: Display edit-page feedback messages](#step-166)
- [Step 167: Message Styling](#step-167)
- [Step 168: Display employee-list messages](#step-168)

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


---

<a id="step-166"></a>

### Step 166: Display edit-page feedback messages

#### Before this step

An employee edit page could also need to show feedback, such as why saving failed. Immediately before this, Step 165 dealt with employee management messages; the work here continued the same part of the application.

#### What we did and why

We made sure the edit template had a place to show messages or field errors. This helps the person know whether they need to correct something.

#### Code and commands

**Step 166, block 1.** This code sample or terminal command was used while working on display edit-page feedback messages. The content below remains in its original sequence for Step 166, block 1.

```html
{% if employee.is_active and not employee.has_usable_password %}

    <form method="POST"
          action="{% url 'resend_setup_email' employee.id %}">

        {% csrf_token %}

        <button type="submit">
            Resend Setup Email
        </button>

    </form>

{% endif %}
```

#### How the code works

**Block 1 (html, 14 lines).** In Step 166, display edit-page feedback messages involved `edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if employee.is_active and not employee.has_usable_password %}` decides which HTML is included based on the value Django provided. The form sends submitted fields to the server as a POST request, rather than just opening a page. `action="{% url 'resend_setup_email' employee.id %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 8–14:** The HTML element `<button type="submit">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 166. For Step 166, the proposed way to check display edit-page feedback messages was to open the related page in the browser and check that the route reached the intended view or template in `edit_employee.html`. Development continued in Step 167 before a complete result could be claimed.

#### Next

[Step 165: Employee Management Messages](#step-165) · [Step 167: Message Styling](#step-167)


---

<a id="step-167"></a>

### Step 167: Message Styling

#### Before this step

Success and error notices looked too similar, so the administrator could miss an important warning. Immediately before this, Step 166 dealt with display edit-page feedback messages; the work here continued the same part of the application.

#### What we did and why

We added styling that makes the type of message more visible. CSS changes how notices look; the view still decides which notice to show.

#### Code and commands

**Step 167, block 1.** This code sample or terminal command was used while working on message styling. The content below remains in its original sequence for Step 167, block 1.

```html
{% if messages %}

    {% for message in messages %}

        <p class="message {{ message.tags }}">
            {{ message }}
        </p>

    {% endfor %}

{% endif %}
```

**Step 167, block 2.** This code sample or terminal command was used while working on message styling. The content below remains in its original sequence for Step 167, block 2.

```html
<p>{{ message }}</p>
```

**Step 167, block 3.** This code sample or terminal command was used while working on message styling. The content below remains in its original sequence for Step 167, block 3.

```html
<p class="message {{ message.tags }}">
    {{ message }}
</p>
```

**Step 167, block 4.** This code sample or terminal command was used while working on message styling. The content below remains in its original sequence for Step 167, block 4.

```html
  <p class="message success">
      Employee updated successfully.
  </p>
  
```

#### How the code works

**Block 1 (html, 11 lines).** In Step 167, message styling involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if messages %}` decides which HTML is included based on the value Django provided. `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p class="message {{ message.tags }}">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 8–11:** The lines starting with `{% endfor %}` supply the values or page content for this part of templates/accounts/employee_list.html. These lines are kept together because their order and indentation matter.

**Block 2 (html, 1 lines).** In Step 167, message styling involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The HTML element `<p>{{ message }}</p>` creates or closes part of the page structure seen by the browser.

**Block 3 (html, 3 lines).** In Step 167, message styling involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** The HTML element `<p class="message {{ message.tags }}">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Block 4 (html, 4 lines).** In Step 167, message styling involved `templates/accounts/employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** The HTML element `<p class="message success">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 167. For Step 167, the proposed way to check message styling was to revisit `templates/accounts/employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 168 before a complete result could be claimed.

#### Next

[Step 166: Display edit-page feedback messages](#step-166) · [Step 168: Display employee-list messages](#step-168)


---

<a id="step-168"></a>

### Step 168: Display employee-list messages

#### Before this step

The employee list is where many actions return after saving. It needed a reliable place to show the result. Immediately before this, Step 167 dealt with message styling; the work here continued the same part of the application.

#### What we did and why

The updated list template displays Django's messages near the employee information. A “saved” notice now has somewhere to appear after the redirect.

#### Code and commands

**Step 168, block 1.** This code sample or terminal command was used while working on display employee-list messages. The content below remains in its original sequence for Step 168, block 1.

```html
{% if messages %}
    {% for message in messages %}
        <p class="message {{ message.tags }}">
            {{ message }}
        </p>
    {% endfor %}
{% endif %}
```

**Step 168, block 2.** This code sample or terminal command was used while working on display employee-list messages. The content below remains in its original sequence for Step 168, block 2.

```html
<body>

    <h2>Manage Employees</h2>

    {% if messages %}
        {% for message in messages %}
            <p class="message {{ message.tags }}">
                {{ message }}
            </p>
        {% endfor %}
    {% endif %}

    <p>
        <a href="{% url 'create_employee' %}">
            Add New Employee
        </a>
    </p>
```

#### How the code works

**Block 1 (html, 7 lines).** In Step 168, display employee-list messages involved `employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if messages %}` decides which HTML is included based on the value Django provided. `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p class="message {{ message.tags }}">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Block 2 (html, 17 lines).** In Step 168, display employee-list messages involved `employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<body>` creates or closes part of the page structure seen by the browser. The HTML element `<h2>Manage Employees</h2>` creates or closes part of the page structure seen by the browser. `{% if messages %}` decides which HTML is included based on the value Django provided. `{% for message in messages %}` repeats the following operation once for each matching item.

**Lines 8–14:** The HTML element `</p>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'create_employee' %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 15–17:** The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 168. For Step 168, the proposed way to check display employee-list messages was to revisit `employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 169 before a complete result could be claimed.

#### Next

[Step 167: Message Styling](#step-167) · [Step 169: Message Styling CSS](#step-169)
