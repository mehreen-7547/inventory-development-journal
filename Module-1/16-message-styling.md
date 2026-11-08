# MODULE 1 — Accounts

## Chapter 16: Message styling (Steps 165–169)

**Where we are.** The underlying actions worked, but users could miss success and failure notifications.

**What this chapter gives us.** Render Django messages consistently and apply shared CSS styles.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain how a message set in a view becomes visible after redirect.

### Find a step

- [Step 165: Employee Management Messages](#step-165)
- [Step 166: Display edit-page feedback messages](#step-166)
- [Step 167: Message Styling](#step-167)
- [Step 168: Display employee-list messages](#step-168)
- [Step 169: Message Styling CSS](#step-169)

---

<a id="step-165"></a>

### Step 165: Employee Management Messages

**Why we are doing this.** Employee create/edit/status actions set feedback messages, but the destination pages must actually display them.

**File / location.** `accounts/views.py and templates`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```html
<h2>Manage Employees</h2>
```

*2. Recorded snippet; Add.*

```html
{% if messages %}

    {% for message in messages %}

        <p>{{ message }}</p>

    {% endfor %}

{% endif %}
```

**How it works.** Django's `messages` loop renders success and error feedback after redirect. It connects backend actions to visible results.

**How we checked it.** The next steps place message display into the edit/list templates and style it.

**Connects to.** Previous: [Step 164 — Home Template Review](15-activate-deactivate-and-resend-setup-email.md#step-164) · Next: [Step 166 — Display edit-page feedback messages](16-message-styling.md#step-166)


---

<a id="step-166"></a>

### Step 166: Display edit-page feedback messages

**Why we are doing this.** The edit page also needs a consistent way to show validation or save feedback.

**File / location.** `templates/accounts/edit_employee.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In templates/accounts/editemployee.html, add this immediately after the Deactivate/Reactivate form's closing </form>.*

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

**How it works.** This template update places the appropriate message rendering near the form so users know whether changes succeeded.

The token helps Django reject forged state-changing form submissions.

This flag allows deactivation without deleting the user’s record and associated history.

**How we checked it.** The shared styling work follows; test a successful edit and an invalid form submission.

**Connects to.** Previous: [Step 165 — Employee Management Messages](16-message-styling.md#step-165) · Next: [Step 167 — Message Styling](16-message-styling.md#step-167)


---

<a id="step-167"></a>

### Step 167: Message Styling

**Why we are doing this.** Plain messages were difficult to distinguish visually, especially errors versus successful actions.

**File / location.** `template message markup`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Find the messages section and replace it with.*

```html
{% if messages %}

    {% for message in messages %}

        <p class="message {{ message.tags }}">
            {{ message }}
        </p>

    {% endfor %}

{% endif %}
```

*2. Recorded snippet.*

```html
<p>{{ message }}</p>
```

*3. Recorded snippet; Now.*

```html
<p class="message {{ message.tags }}">
    {{ message }}
</p>
```

*4. Recorded snippet; For example, a successful update produces.*

```html
  <p class="message success">
      Employee updated successfully.
  </p>
  
```

**How it works.** The markup adds a message-type class so CSS can style each Django message level. The backend message text remains unchanged.

**How we checked it.** Later steps apply this pattern consistently across employee pages.

**Connects to.** Previous: [Step 166 — Display edit-page feedback messages](16-message-styling.md#step-166) · Next: [Step 168 — Display employee-list messages](16-message-styling.md#step-168)


---

<a id="step-168"></a>

### Step 168: Display employee-list messages

**Why we are doing this.** The employee list is a common redirect destination, so it must display action outcomes near its heading.

**File / location.** `templates/accounts/employee_list.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```html
{% if messages %}
    {% for message in messages %}
        <p class="message {{ message.tags }}">
            {{ message }}
        </p>
    {% endfor %}
{% endif %}
```

*2. Recorded snippet; The beginning of your <body> should now look like this.*

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

**How it works.** The list template renders messages with classes such as `success` or `error`, preparing them for stylesheet rules.

**How we checked it.** The next step adds the CSS that makes these classes visible.

**Connects to.** Previous: [Step 167 — Message Styling](16-message-styling.md#step-167) · Next: [Step 169 — Message Styling CSS](16-message-styling.md#step-169)


---

<a id="step-169"></a>

### Step 169: Message Styling CSS

**Why we are doing this.** The message markup existed but lacked a consistent visual treatment.

**File / location.** `accounts/static/accounts/style.css`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add this code at the bottom, keeping your existing CSS unchanged.*

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

**How it works.** The external stylesheet defines padding, borders, and styles for success/error messages. Styling does not change which actions are permitted.

**How we checked it.** Verify both a successful and a failed operation display visibly distinct notices.

**Connects to.** Previous: [Step 168 — Display employee-list messages](16-message-styling.md#step-168) · Next: [Step 170 — Real Email Configuration](17-real-email-delivery-smtp.md#step-170)

---

**Chapter recap.** Render Django messages consistently and apply shared CSS styles.

**Practice explaining:** Explain how a message set in a view becomes visible after redirect.

