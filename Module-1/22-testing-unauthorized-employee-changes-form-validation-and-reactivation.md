# Module 1 — Employee Accounts

## Chapter 22 — Testing Unauthorized Employee Changes, Form Validation and Account Reactivation

**Journal steps 217–223 · Code commit `ab32660`**

### Starting situation

Chapter 21 established twelve employee-management tests, giving 23 passing project tests. We still needed explicit checks for unauthorized POST requests, employee reactivation, invalid creation/edit forms and setup-email restrictions. The production implementation already included admin-only decorators and form validation; this chapter tests those paths rather than adding new production features.

### Real-world scenario

Imagine a small shop where a sales employee tries to disable a colleague by manually sending a POST request, a manager attempts to edit staff, and an administrator accidentally enters an email already assigned to someone else. The system should reject unauthorized or invalid changes without silently modifying records. An administrator must also be able to reactivate a returning employee, while setup emails must obey account status and HTTP method rules.

### Chapter navigation

- [Step 217 — Prepare three roles and reusable employee form data](#step-217)
- [Step 218 — Prevent non-admin POST requests from changing employees](#step-218)
- [Step 219 — Reactivate staff while protecting administrator accounts](#step-219)
- [Step 220 — Reject duplicate emails, missing usernames and admin roles](#step-220)
- [Step 221 — Preserve existing emails and enforce setup-email rules](#step-221)
- [Step 222 — Run all 35 tests in an isolated Django database](#step-222)
- [Step 223 — Commit the passing tests and verify the GitHub branch](#step-223)

---

<a id="step-217"></a>
### Step 217 — Prepare three roles and reusable employee form data

**Why this step matters:** The inventory system has an administrator, a manager and a sales employee. A permission test needs to simulate each role without depending on a browser session or production database. We added a separate test class with its own test users so the tests cannot accidentally inherit another test's changes. Reusable dictionaries also keep every POST request consistent when only the username, email or role is supposed to change.

**Filename / location:** `accounts/tests.py` — `EmployeePermissionValidationTests`

```python
class EmployeePermissionValidationTests(TestCase):
    def setUp(self):
        self.admin = CustomUser.objects.create_user(
            username='admincheck', email='admincheck@example.com',
            password='AdminPass123!', role='admin'
        )
        self.employee = CustomUser.objects.create_user(
            username='salescheck', email='salescheck@example.com',
            password='SalesPass123!', role='sales'
        )
        self.manager = CustomUser.objects.create_user(
            username='managercheck', email='managercheck@example.com',
            password='ManagerPass123!', role='manager'
        )
        self.client.force_login(self.admin)

    def employee_data(self, username='newstaff',
                      email='newstaff@example.com', role='sales'):
        return {
            'username': username, 'first_name': 'New',
            'last_name': 'Staff', 'email': email, 'role': role
        }

    def edit_data(self, email='salescheck@example.com', role='sales'):
        return {
            'first_name': 'Changed', 'last_name': 'Staff',
            'email': email, 'role': role
        }
```

**Code and workflow walkthrough:** `TestCase` creates an isolated test environment. `setUp` runs before each test and creates three accounts. `force_login` begins with an admin session. `employee_data` and `edit_data` return valid default form fields; individual tests override only the field relevant to their scenario. These are test-only email addresses and passwords.

**Verified evidence and limits:** GitHub `accounts/tests.py` at commit `ab32660` contains this class and its helper methods.

**Navigation:** [Previous chapter, Step 216](21-testing-employee-management-and-admin-permissions.md#step-216) · [Next, Step 218](#step-218)

---

<a id="step-218"></a>
### Step 218 — Prevent non-admin POST requests from changing employees

**Why this step matters:** It is not enough to hide an employee-management link from sales or managers. Someone could manually send an HTTP POST to the employee-creation, editing, status or resend endpoint. We therefore tested unauthorized submissions to all four routes. The key assertion is not just the redirect: the attempted database modification or email side effect must also be absent.

**Filename / location:** `accounts/tests.py` — four unauthorized modification tests

```python
@patch('accounts.views.send_welcome_email')
def test_sales_cannot_create_employee(self, mock_email):
    self.client.force_login(self.employee)
    response = self.client.post(
        reverse('create_employee'), self.employee_data()
    )
    self.assertRedirects(response, reverse('home'))
    self.assertFalse(
        CustomUser.objects.filter(username='newstaff').exists()
    )
    mock_email.assert_not_called()

def test_manager_cannot_edit_employee(self):
    self.client.force_login(self.manager)
    response = self.client.post(
        reverse('edit_employee', args=[self.employee.pk]),
        self.edit_data()
    )
    self.assertRedirects(response, reverse('home'))
    self.employee.refresh_from_db()
    self.assertEqual(self.employee.first_name, '')

def test_sales_cannot_deactivate_employee(self):
    self.client.force_login(self.employee)
    response = self.client.post(
        reverse('toggle_employee_status', args=[self.manager.pk])
    )
    self.assertRedirects(response, reverse('home'))
    self.manager.refresh_from_db()
    self.assertTrue(self.manager.is_active)

@patch('accounts.views.send_welcome_email')
def test_manager_cannot_resend_setup_email(self, mock_email):
    self.employee.set_unusable_password()
    self.employee.save()
    self.client.force_login(self.manager)
    response = self.client.post(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('home'))
    mock_email.assert_not_called()
```

**Code and workflow walkthrough:** `self.client.post` simulates a real HTTP POST. `reverse` resolves the named route. `force_login` switches from admin to a non-admin account. `refresh_from_db` confirms the saved record did not change. `patch` intercepts email sending and `assert_not_called` checks that no email attempt occurred. The production `role_required('admin')` decorator handles these rejections.

**Verified evidence and limits:** All four unauthorized POST tests passed in the user's 35-test run. They verify the four sampled combinations of non-admin role and endpoint, not every possible role-endpoint pairing.

**Navigation:** [Previous, Step 217](#step-217) · [Next, Step 219](#step-219)

---

<a id="step-219"></a>
### Step 219 — Reactivate staff while protecting administrator accounts

**Why this step matters:** A deactivated employee may return to work, so the admin must be able to reactivate the same account instead of creating a duplicate. The status view toggles `is_active`, meaning a POST on an inactive account should restore it. The employee-editing route must also reject administrator records, because its purpose is to manage manager and sales accounts, not to change administrator details.

**Filename / location:** `accounts/tests.py` — reactivation and admin-edit protection tests

```python
def test_admin_can_reactivate_employee(self):
    self.employee.is_active = False
    self.employee.save()
    response = self.client.post(
        reverse('toggle_employee_status', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    self.employee.refresh_from_db()
    self.assertTrue(self.employee.is_active)

def test_admin_cannot_edit_admin_using_employee_form(self):
    response = self.client.post(
        reverse('edit_employee', args=[self.admin.pk]),
        self.edit_data()
    )
    self.assertEqual(response.status_code, 404)
    self.admin.refresh_from_db()
    self.assertEqual(self.admin.first_name, '')
```

**Code and workflow walkthrough:** The first test saves `is_active=False` before posting, then checks it becomes `True`. The second supplies the admin's primary key to the employee-edit URL. The production view uses `get_object_or_404` with `role__in=['manager', 'sales']`, so it returns 404 for an administrator instead of editing that account.

**Verified evidence and limits:** Both tests passed. Reactivation is verified for an admin acting on a sales account; no bulk reactivation functionality is implied.

**Navigation:** [Previous, Step 218](#step-218) · [Next, Step 220](#step-220)

---

<a id="step-220"></a>
### Step 220 — Reject duplicate emails, missing usernames and admin roles

**Why this step matters:** Employee creation should not silently accept a duplicate email or a missing username. It also must not allow the staff creation form to grant the administrator role, even if a caller manually changes the submitted role field. These tests submit invalid form data directly and verify that no employee was created. An invalid form returns the form page instead of redirecting to the employee list.

**Filename / location:** `accounts/tests.py` — three invalid creation tests

```python
@patch('accounts.views.send_welcome_email')
def test_duplicate_email_cannot_create_employee(self, mock_email):
    response = self.client.post(
        reverse('create_employee'),
        self.employee_data(email='salescheck@example.com')
    )
    self.assertEqual(response.status_code, 200)
    self.assertFalse(
        CustomUser.objects.filter(username='newstaff').exists()
    )
    mock_email.assert_not_called()

@patch('accounts.views.send_welcome_email')
def test_admin_role_cannot_be_created_from_employee_form(
    self, mock_email
):
    response = self.client.post(
        reverse('create_employee'), self.employee_data(role='admin')
    )
    self.assertEqual(response.status_code, 200)
    self.assertFalse(
        CustomUser.objects.filter(username='newstaff').exists()
    )
    mock_email.assert_not_called()

@patch('accounts.views.send_welcome_email')
def test_missing_username_cannot_create_employee(
    self, mock_email
):
    response = self.client.post(
        reverse('create_employee'), self.employee_data(username='')
    )
    self.assertEqual(response.status_code, 200)
    self.assertFalse(
        CustomUser.objects.filter(email='newstaff@example.com').exists()
    )
    mock_email.assert_not_called()
```

**Code and workflow walkthrough:** The model declares `email` unique, while the creation form restricts role choices to manager and sales. The username is a required model form field. HTTP 200 here means the view rendered the form again, not that creation succeeded. Database existence assertions and the email mock make the tests more meaningful than checking status alone.

**Verified evidence and limits:** All three tests passed. The tests do not assert the exact form error messages displayed to a user.

**Navigation:** [Previous, Step 219](#step-219) · [Next, Step 221](#step-221)

---

<a id="step-221"></a>
### Step 221 — Preserve existing emails and enforce setup-email rules

**Why this step matters:** Editing one employee to use another employee's email would break the system's unique-email requirement. Likewise, a setup email should not be resent to an employee who already has a usable password. The resend endpoint must accept POST rather than GET to prevent a simple page visit from triggering an email. We added a test for each rule using the existing accounts and mocked email sender.

**Filename / location:** `accounts/tests.py` — duplicate-edit and setup-email restriction tests

```python
def test_duplicate_email_cannot_update_employee(self):
    response = self.client.post(
        reverse('edit_employee', args=[self.employee.pk]),
        self.edit_data(email='managercheck@example.com')
    )
    self.assertEqual(response.status_code, 200)
    self.employee.refresh_from_db()
    self.assertEqual(self.employee.email, 'salescheck@example.com')

@patch('accounts.views.send_welcome_email')
def test_existing_password_prevents_setup_email(self, mock_email):
    response = self.client.post(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    mock_email.assert_not_called()

@patch('accounts.views.send_welcome_email')
def test_resend_setup_email_requires_post(self, mock_email):
    response = self.client.get(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertEqual(response.status_code, 405)
    mock_email.assert_not_called()
```

**Code and workflow walkthrough:** `refresh_from_db` proves the invalid edit did not overwrite the saved email. The existing password is usable because `create_user` set it in `setUp`; the resend view therefore skips the email. `@require_POST` returns HTTP 405 on GET. The mock verifies the email sender was not called in either prohibited resend case.

**Verified evidence and limits:** All three tests passed. SMTP delivery itself was not tested, because the email function was intentionally mocked.

**Navigation:** [Previous, Step 220](#step-220) · [Next, Step 222](#step-222)

---

<a id="step-222"></a>
### Step 222 — Run all 35 tests in an isolated Django database

**Why this step matters:** A permission-focused test suite can accidentally break earlier login, logout or database behaviour. We therefore ran the full Django test command, which discovers tests across the project, creates a separate test database and reports every failing assertion. The output is the concrete evidence for the chapter's results. It should be recorded accurately rather than replacing the actual timing or result with an estimate.

**Filename / location:** Project root — activated PowerShell virtual environment

```powershell
python manage.py test

# Observed console output:
# Found 35 test(s).
# Creating test database for alias 'default'...
# System check identified no issues (0 silenced).
# Ran 35 tests in 28.061s
# OK
# Destroying test database for alias 'default'...
```

**Code and workflow walkthrough:** The `test` command runs all discovered Django tests. The reported 35 comprise five Core tests and thirty accounts tests: six authentication, twelve initial employee-management and twelve additional permission/validation tests. `OK` indicates all executed assertions passed. The test database is destroyed afterward, so this command does not modify the development database.

**Verified evidence and limits:** The user supplied the complete console output with 35 discovered tests, 28.061 seconds and `OK`. This is a local execution result, not a GitHub Actions or production verification.

**Navigation:** [Previous, Step 221](#step-221) · [Next, Step 223](#step-223)

---

<a id="step-223"></a>
### Step 223 — Commit the passing tests and verify the GitHub branch

**Why this step matters:** The final step is to preserve the tested code as a named Git revision. Without a push, the local changes would not be available to the journal reader or a reviewer inspecting the repository. We staged only `accounts/tests.py`, committed the additional permission tests and pushed to `main`. We then checked the GitHub branch and test file independently before writing this chapter.

**Filename / location:** Project root — PowerShell; GitHub code repository `mehreen-7547/ai-assisted-inventory-manager`

```powershell
git add accounts/tests.py
git commit -m "Add employee permission and validation tests"
git push origin main
git status --short
git log -1 --oneline

# Verified commit:
# ab32660 Add employee permission and validation tests
```

**Code and workflow walkthrough:** `git add` stages the changed test file. `git commit` records it locally and `git push` publishes it. `git status --short` produced no changed-file entries in the supplied output, indicating a clean working tree. The final `git log` shows `HEAD -> main, origin/main`, and GitHub's `main` branch resolves to full SHA `ab326607a4aef4e07f96f986f3670dfd90a77c30`.

**Verified evidence and limits:** GitHub was checked directly and contains the additional class and its twelve tests. The commit adds 195 lines to `accounts/tests.py`; production views and forms were not changed in this step.

**Navigation:** [Previous, Step 222](#step-222)

---

### What this chapter proves

The code repository contains twelve additional tests for unauthorized POST actions, account reactivation, protection of admin accounts, invalid employee creation/editing and password-setup email restrictions. All 35 project tests passed locally. These tests are evidence for their specific cases, not a comprehensive penetration test or proof of real email delivery. This chapter documents tests; it does not claim changes to the production views.

### Recall question

Why is a redirect assertion alone insufficient when testing whether a sales employee can create another employee? Which database and email assertions make the test stronger?
