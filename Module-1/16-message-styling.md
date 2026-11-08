# Module 1, Chapter 16: Message styling

## Step 165: Employee Management Messages

#### Next: Display success and error messages

Our employee management views already generate messages such as:

- Employee created successfully.
- Employee updated successfully.
- Employee deactivated successfully.
- Password setup email generated successfully.

However, our **Manage Employees** page doesn't currently display them.

#### Step 1: Update the employee list template

Open **`templates/accounts/employee_list.html`**.

Immediately below:

```html
<h2>Manage Employees</h2>
```

Add:

```html
{% if messages %}

    {% for message in messages %}

        <p>{{ message }}</p>

    {% endfor %}

{% endif %}
```

#### Step 2: Test

1. Open **Manage Employees**.
2. Click **Edit Employee** and select an employee.
3. Change their first name and click **Save Changes**.
4. You should return to the employee list and see:

  Manage Employees

    Employee updated successfully.

This also allows the page to display feedback after employee creation, deactivation, reactivation and resending setup emails.

**Save and test.** Next, we'll improve the messages using our external CSS stylesheet.

*Previous: Step 164 (Home Template Review)  |  Next: Step 166 (Change edit_employee.html)*

---

## Step 166: Change edit_employee.html

Your `edit_employee.html` is correct for **editing employee details and deactivating/reactivating accounts**.

However, the **Resend Setup Email** button from Step 275 is missing.

#### 1. Add the missing button

In `templates/accounts/edit_employee.html`, add this immediately after the Deactivate/Reactivate form's closing `</form>`:

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

#### 2. Regarding Step 279

The success/error messages code belongs in **`templates/accounts/employee_list.html`**, not `edit_employee.html`.

Add it immediately below `<h2>Manage Employees</h2>` in that file.

Everything else in the code you shared can remain unchanged.

*Previous: Step 165 (Employee Management Messages)  |  Next: Step 167 (Message Styling)*

---

## Step 167: Message Styling

#### Next: Prepare success and error messages for CSS

We want success messages to appear in green and error messages in red, using our external stylesheet.

Open **`templates/accounts/employee_list.html`**.

Find the messages section and replace it with:

```html
{% if messages %}

    {% for message in messages %}

        <p class="message {{ message.tags }}">
            {{ message }}
        </p>

    {% endfor %}

{% endif %}
```

#### What changed?

Previously:

```html
<p>{{ message }}</p>
```

Now:

```html
<p class="message {{ message.tags }}">
    {{ message }}
</p>
```

Django automatically provides message tags such as `success`, `error`, `warning` and `info`.

  **NEW vs CLASS — `message.tags`**

  Django's messages framework identifies the type of message.

  For example, a successful update produces:

  ```html
  <p class="message success">
      Employee updated successfully.
  </p>
  
```

  We can use these CSS classes to apply different styles.

**Save the HTML file.** No visual changes are expected yet.

Next, we'll add the styles to `accounts/static/accounts/style.css`.

*Previous: Step 166 (Change edit_employee.html)  |  Next: Step 168 (Change employee_list.html)*

---

## Step 168: Change employee_list.html

Your code is correct, but the **messages section is missing**. Add it immediately below `<h2>Manage Employees</h2>`.

#### Add this to `employee_list.html`

```html
{% if messages %}
    {% for message in messages %}
        <p class="message {{ message.tags }}">
            {{ message }}
        </p>
    {% endfor %}
{% endif %}
```

The beginning of your `<body>` should now look like this:

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

**Everything else in your file can remain unchanged.**

Next, we'll style these messages in the existing external CSS file.

*Previous: Step 167 (Message Styling)  |  Next: Step 169 (Message Styling CSS)*

---

## Step 169: Message Styling CSS

#### Next: Style success and error messages

We've added the messages section to `employee_list.html`. Now we'll style it using our external CSS file.

Open **`accounts/static/accounts/style.css`**.

Add this code at the bottom, keeping your existing CSS unchanged:

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

#### Expected appearance

    Employee updated successfully.

    Cannot send setup email to an inactive employee.

  **NEW vs CLASS — `.message.success`**

  This CSS selector targets an element that has **both** the `message` and `success` classes.

  Django supplies the `success` class through `{{ message.tags }}`.

#### Test

1. Save `style.css`.
2. Open Manage Employees and edit an employee.
3. Click **Save Changes**.
4. Verify that the success message appears with green styling.

Next, we'll verify the Resend Setup Email workflow.

*Previous: Step 168 (Change employee_list.html)  |  Next: Step 170 (Real Email Configuration)*

---
