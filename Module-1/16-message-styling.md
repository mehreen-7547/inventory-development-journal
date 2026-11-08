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
