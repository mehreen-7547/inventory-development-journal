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
