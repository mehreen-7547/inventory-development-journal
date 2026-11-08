# MODULE 1 — Employee accounts

## Chapter 16: Message styling (Steps 165–169)

### Before we begin

The account-management views were saving changes and setting messages, but those messages were not always easy to see.

### What we worked on

We gave the employee screens clear places to show success and error messages, then added styling so the notices would stand out.

### Picture it this way

When a view saves a change and redirects back to a list, Django can carry a short message to the next page. The destination page must display that message, otherwise the user does not know what happened.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 165: Employee Management Messages](#step-165)
- [Step 166: Display edit-page feedback messages](#step-166)
- [Step 167: Message Styling](#step-167)
- [Step 168: Display employee-list messages](#step-168)
- [Step 169: Message Styling CSS](#step-169)

---

<a id="step-165"></a>

### Step 165: Employee Management Messages

**What was happening before this step?**

The create and edit views could set success or error messages, but a message is useless if the next page never shows it.

**What did we do, and what does it mean?**

We added message display to the employee-management page. Django carries a short notice across the redirect, and the template prints it for the administrator.

**File or place:** `accounts/views.py and templates`.

**Code or command from this step**

*1. Code or command used at this point.*

```html
<h2>Manage Employees</h2>
```

*2. Add.*

```html
{% if messages %}

    {% for message in messages %}

        <p>{{ message }}</p>

    {% endfor %}

{% endif %}
```

**Understanding the code, one piece at a time**

A view can set a message before redirecting to the list page. The list template then displays it. Without that display code, the message exists but the administrator never sees it.

**How did we know what happened?**

The next steps place message display into the edit/list templates and style it.

**Where to go next:** Previous: [Step 164 — Home Template Review](15-activate-deactivate-and-resend-setup-email.md#step-164) · Next: [Step 166 — Display edit-page feedback messages](16-message-styling.md#step-166)


---

<a id="step-166"></a>

### Step 166: Display edit-page feedback messages

**What was happening before this step?**

An employee edit page could also need to show feedback, such as why saving failed.

**What did we do, and what does it mean?**

We made sure the edit template had a place to show messages or field errors. This helps the person know whether they need to correct something.

**File or place:** `templates/accounts/edit_employee.html`.

**Code or command from this step**

*1. In templates/accounts/editemployee.html, add this immediately after the Deactivate/Reactivate form's closing </form>.*

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

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

The shared styling work follows; test a successful edit and an invalid form submission.

**Where to go next:** Previous: [Step 165 — Employee Management Messages](16-message-styling.md#step-165) · Next: [Step 167 — Message Styling](16-message-styling.md#step-167)


---

<a id="step-167"></a>

### Step 167: Message Styling

**What was happening before this step?**

Success and error notices looked too similar, so the administrator could miss an important warning.

**What did we do, and what does it mean?**

We added styling that makes the type of message more visible. CSS changes how notices look; the view still decides which notice to show.

**File or place:** `template message markup`.

**Code or command from this step**

*1. Find the messages section and replace it with.*

```html
{% if messages %}

    {% for message in messages %}

        <p class="message {{ message.tags }}">
            {{ message }}
        </p>

    {% endfor %}

{% endif %}
```

*2. Code or command used at this point.*

```html
<p>{{ message }}</p>
```

*3. Now.*

```html
<p class="message {{ message.tags }}">
    {{ message }}
</p>
```

*4. For example, a successful update produces.*

```html
  <p class="message success">
      Employee updated successfully.
  </p>
  
```

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

Later steps apply this pattern consistently across employee pages.

**Where to go next:** Previous: [Step 166 — Display edit-page feedback messages](16-message-styling.md#step-166) · Next: [Step 168 — Display employee-list messages](16-message-styling.md#step-168)


---

<a id="step-168"></a>

### Step 168: Display employee-list messages

**What was happening before this step?**

The employee list is where many actions return after saving. It needed a reliable place to show the result.

**What did we do, and what does it mean?**

The updated list template displays Django's messages near the employee information. A “saved” notice now has somewhere to appear after the redirect.

**File or place:** `templates/accounts/employee_list.html`.

**Code or command from this step**

*1. Code or command used at this point.*

```html
{% if messages %}
    {% for message in messages %}
        <p class="message {{ message.tags }}">
            {{ message }}
        </p>
    {% endfor %}
{% endif %}
```

*2. The beginning of your <body> should now look like this.*

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

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

The next step adds the CSS that makes these classes visible.

**Where to go next:** Previous: [Step 167 — Message Styling](16-message-styling.md#step-167) · Next: [Step 169 — Message Styling CSS](16-message-styling.md#step-169)


---

<a id="step-169"></a>

### Step 169: Message Styling CSS

**What was happening before this step?**

The message text was showing, but we wanted it to be easier to read and recognise.

**What did we do, and what does it mean?**

The CSS file gives notices consistent spacing and appearance. This makes feedback clearer without changing the rules for creating or editing employees.

**File or place:** `accounts/static/accounts/style.css`.

**Code or command from this step**

*1. Add this code at the bottom, keeping your existing CSS unchanged.*

```css
.message {
    padding: 10px;
    margin: 15px 0;
    border-radius: 5px;
    border: 1px solid;
}

.message.success {
    color: #155724;
    background-color: #d4edda;
    border-color: #c3e6cb;
}

.message.error {
    color: #721c24;
    background-color: #f8d7da;
    border-color: #f5c6cb;
}

.message.warning {
    color: #856404;
    background-color: #fff3cd;
    border-color: #ffeeba;
}
```

**How did we know what happened?**

Verify both a successful and a failed operation display visibly distinct notices.

**Where to go next:** Previous: [Step 168 — Display employee-list messages](16-message-styling.md#step-168) · Next: [Step 170 — Real Email Configuration](17-real-email-delivery-smtp.md#step-170)

---

### The point of this chapter

When a view saves a change and redirects back to a list, Django can carry a short message to the next page. The destination page must display that message, otherwise the user does not know what happened.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

