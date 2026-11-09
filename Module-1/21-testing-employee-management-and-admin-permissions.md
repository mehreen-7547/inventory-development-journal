# MODULE 1 — Employee accounts

## Chapter 21: Testing Employee Creation, Editing, Deactivation, Administrator Permissions and Setup Emails (Steps 210–216)

### Before we begin

The accounts module already had admin-only employee list, creation, editing, status toggling and setup-email resend views. Chapter 20 had tested login and logout, but not these employee-management workflows.

### What we worked on

We added twelve tests using three role-specific test accounts, mocked email sending and a temporary Django test database. We ran all 23 tests, checked remaining coverage gaps and committed the test file.

### Practical example

Imagine a store with an administrator, inventory manager and sales employee. The administrator hires and disables staff, while other employees must not use the administration pages. The system also sends password-setup emails and must cope with an email server failure without losing the employee record.

### Jump to a step

- [Step 210: Prepare administrator, manager and sales accounts](#step-210)
- [Step 211: Verify employee listing and reject non-admin access](#step-211)
- [Step 212: Create an employee and handle welcome-email failure](#step-212)
- [Step 213: Edit and deactivate staff without modifying administrators](#step-213)
- [Step 214: Resend password-setup emails safely](#step-214)
- [Step 215: Run the full Django suite and review coverage limits](#step-215)
- [Step 216: Publish the tested employee-management code](#step-216)

---

<a id="step-210"></a>
### Step 210: Prepare administrator, manager and sales accounts

**Why this step was necessary:** The project has three roles: admin, manager and sales. Employee management is intended for admins, so tests must create users with different roles rather than repeatedly signing in as the same person. The test client is signed in as the admin by default, making each management test start from an authorized state. Individual tests can deliberately switch to a manager or sales account to check access restrictions.

**Filename / location before the code:** `accounts/tests.py` — `EmployeeManagementTests.setUp()`

```python
class EmployeeManagementTests(TestCase):
    def setUp(self):
        self.admin = CustomUser.objects.create_user(
            username='adminuser', email='admin@example.com',
            password='AdminPass123!', role='admin'
        )
        self.employee = CustomUser.objects.create_user(
            username='salesuser', email='sales@example.com',
            password='SalesPass123!', role='sales'
        )
        self.manager = CustomUser.objects.create_user(
            username='manageruser', email='manager@example.com',
            password='ManagerPass123!', role='manager'
        )
        self.client.force_login(self.admin)
```

**Walkthrough:** `create_user` saves three separate users in Django's isolated test database. `role` determines what the custom `role_required('admin')` decorator allows. `force_login` avoids repeating login-form submission in every test. The example passwords are test fixtures, not production credentials.

**Observed evidence and limits:** The committed class contains these three accounts and default admin login. The user ran the complete 23-test suite successfully.

**Navigation:** Previous: [Step 209](20-testing-login-logout-inactive-accounts-and-sessions.md#step-209) · Next: [Step 211](#step-211)

---

<a id="step-211"></a>
### Step 211: Verify employee listing and reject non-admin access

**Why this step was necessary:** An administrator should see staff accounts without the admin account appearing as an ordinary employee. A sales user should not be able to open the employee list, and a manager should not be able to open the employee-creation page. We tested both what an authorized admin sees and where unauthorized users are redirected. This matters because hiding navigation buttons alone would not protect a URL.

**Filename / location before the code:** `accounts/tests.py` — `EmployeeManagementTests` listing and permission methods

```python
def test_admin_can_view_employees(self):
    response = self.client.get(reverse('employee_list'))
    self.assertEqual(response.status_code, 200)
    self.assertContains(response, 'salesuser')
    self.assertNotContains(response, 'adminuser')

def test_non_admin_cannot_access_employee_list(self):
    self.client.force_login(self.employee)
    response = self.client.get(reverse('employee_list'))
    self.assertRedirects(response, reverse('home'))

def test_manager_cannot_create_employee(self):
    self.client.force_login(self.manager)
    response = self.client.get(reverse('create_employee'))
    self.assertRedirects(response, reverse('home'))
```

**Walkthrough:** `reverse` uses named URLs rather than hard-coded paths. `assertContains` and `assertNotContains` check rendered page content. `force_login` changes the test session to a non-admin. `assertRedirects` confirms the role decorator's intended response.

**Observed evidence and limits:** The three tests passed. The actual `employee_list` view filters `role__in=['manager', 'sales']`; `role_required('admin')` redirects other roles to `home`. The tests do not yet try unauthorized POST requests.

**Navigation:** Previous: [Step 210](#step-210) · Next: [Step 212](#step-212)

---

<a id="step-212"></a>
### Step 212: Create an employee and handle welcome-email failure

**Why this step was necessary:** An admin can create an employee whose password is initially unusable, then send a setup link by email. We needed to check that the employee record exists even if email delivery fails, because losing a newly created employee would cause inconsistent administration. To keep tests fast and avoid sending real messages, we replaced the email function with a mock during these two tests.

**Filename / location before the code:** `accounts/tests.py` — employee creation tests; production behaviour in `accounts/views.py` and `accounts/forms.py`

```python
@patch('accounts.views.send_welcome_email')
def test_admin_can_create_employee(self, mock_email):
    response = self.client.post(reverse('create_employee'), {
        'username': 'newemployee', 'first_name': 'New',
        'last_name': 'Employee', 'email': 'new@example.com',
        'role': 'sales'
    })
    self.assertRedirects(response, reverse('employee_list'))
    employee = CustomUser.objects.get(username='newemployee')
    self.assertFalse(employee.has_usable_password())
    mock_email.assert_called_once()

@patch('accounts.views.send_welcome_email')
def test_email_failure_keeps_created_employee(self, mock_email):
    mock_email.side_effect = SMTPException('Email failed')
    response = self.client.post(reverse('create_employee'), {
        'username': 'newemployee', 'first_name': 'New',
        'last_name': 'Employee', 'email': 'new@example.com',
        'role': 'sales'
    })
    self.assertRedirects(response, reverse('employee_list'))
    self.assertTrue(CustomUser.objects.filter(username='newemployee').exists())
```

**Walkthrough:** `patch` replaces the function where `accounts.views` uses it. `mock_email.assert_called_once()` verifies one attempted send. `side_effect` simulates an SMTP error. `has_usable_password()` checks that the account cannot use a preset password before setup. The view catches `SMTPException` and `OSError`.

**Observed evidence and limits:** Both creation tests passed in the 23-test run. The tests confirm persistence and the mocked email call, not actual SMTP delivery.

**Navigation:** Previous: [Step 211](#step-211) · Next: [Step 213](#step-213)

---

<a id="step-213"></a>
### Step 213: Edit and deactivate staff without modifying administrators

**Why this step was necessary:** An administrator needs to update employee details and disable an account when staff leave. But the same employee-management endpoint should not modify an admin account, because the view deliberately restricts its target queryset to manager and sales roles. We checked an edit, a successful deactivation, rejection of GET for status changes, and a 404 when trying to change the administrator through the staff endpoint.

**Filename / location before the code:** `accounts/tests.py` — edit and status tests

```python
def test_admin_can_edit_employee(self):
    response = self.client.post(
        reverse('edit_employee', args=[self.employee.pk]),
        {'first_name': 'Updated', 'last_name': 'Employee',
         'email': 'sales@example.com', 'role': 'sales'}
    )
    self.assertRedirects(response, reverse('employee_list'))
    self.employee.refresh_from_db()
    self.assertEqual(self.employee.first_name, 'Updated')

def test_admin_can_deactivate_employee(self):
    response = self.client.post(
        reverse('toggle_employee_status', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    self.employee.refresh_from_db()
    self.assertFalse(self.employee.is_active)

def test_status_change_requires_post(self):
    response = self.client.get(
        reverse('toggle_employee_status', args=[self.employee.pk])
    )
    self.assertEqual(response.status_code, 405)

def test_admin_cannot_deactivate_another_admin(self):
    response = self.client.post(
        reverse('toggle_employee_status', args=[self.admin.pk])
    )
    self.assertEqual(response.status_code, 404)
```

**Walkthrough:** `args=[self.employee.pk]` fills the employee ID in the named URL. `refresh_from_db` reloads saved changes before assertions. HTTP 405 means GET is not allowed; HTTP 404 means the requested admin is outside the permitted employee queryset. `toggle_employee_status` flips `is_active` on POST.

**Observed evidence and limits:** All four tests passed. Reactivation by a second POST and attempts to edit an admin were not separately tested.

**Navigation:** Previous: [Step 212](#step-212) · Next: [Step 214](#step-214)

---

<a id="step-214"></a>
### Step 214: Resend password-setup emails safely

**Why this step was necessary:** An employee who has not set a password may need a new setup email. But a deactivated employee should not receive one, and an SMTP failure should not crash the employee-management page. The tests make the employee's password unusable to represent an account awaiting setup, then use a mock to confirm whether an email was attempted. A separate test forces the mock to raise an exception.

**Filename / location before the code:** `accounts/tests.py` — setup-email resend tests

```python
@patch('accounts.views.send_welcome_email')
def test_resend_setup_email(self, mock_email):
    self.employee.set_unusable_password()
    self.employee.save()
    response = self.client.post(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    mock_email.assert_called_once()

@patch('accounts.views.send_welcome_email')
def test_inactive_employee_gets_no_setup_email(self, mock_email):
    self.employee.is_active = False
    self.employee.set_unusable_password()
    self.employee.save()
    response = self.client.post(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    mock_email.assert_not_called()

@patch('accounts.views.send_welcome_email')
def test_resend_email_failure_is_handled(self, mock_email):
    self.employee.set_unusable_password()
    self.employee.save()
    mock_email.side_effect = SMTPException('Email failed')
    response = self.client.post(
        reverse('resend_setup_email', args=[self.employee.pk])
    )
    self.assertRedirects(response, reverse('employee_list'))
    mock_email.assert_called_once()
```

**Walkthrough:** `set_unusable_password` represents an employee who cannot log in yet. `assert_not_called` proves that inactive staff do not trigger a resend. `side_effect` simulates mail failure without any network request. The production view also checks `has_usable_password()`, but that branch has no dedicated test here.

**Observed evidence and limits:** All three resend tests passed in the 23-test run. The `@require_POST` restriction on resend and the already-set-password case remain untested explicitly.

**Navigation:** Previous: [Step 213](#step-213) · Next: [Step 215](#step-215)

---

<a id="step-215"></a>
### Step 215: Run the full Django suite and review coverage limits

**Why this step was necessary:** A new set of tests must not break previously working authentication or Core behaviour. After appending the 12 employee-management tests to the existing six authentication tests, we ran the whole project suite rather than testing only the new class. This gave one clear checkpoint for the code repository. Passing tests are strong evidence for the paths they exercise, but they are not proof that every permission or validation path is covered.

**Filename / location before the code:** Project root PowerShell terminal

```powershell
python manage.py test

# Observed summary:
# Found 23 test(s).
# System check identified no issues (0 silenced).
# Ran 23 tests in 15.513s
# OK
```

**Walkthrough:** The command discovers tests from the Django apps, uses a temporary test database and reports failures if an assertion or execution raises an error. The result combines five Core tests, six authentication tests and twelve employee-management tests. It is not a production email-delivery test.

**Observed evidence and limits:** The user pasted the console output showing 23 tests, 15.513 seconds, `OK` and destruction of the test database. Remaining explicit coverage gaps: non-admin POST modifications, reactivation, invalid creation/edit forms, and resend when the employee already has a usable password.

**Navigation:** Previous: [Step 214](#step-214) · Next: [Step 216](#step-216)

---

<a id="step-216"></a>
### Step 216: Publish the tested employee-management code

**Why this step was necessary:** Once the test suite passed, the new test file needed to be committed so the repository records exactly what was verified. This matters for academic review: a reader can inspect the code, compare it to the chapter's examples and see that the tests are not merely proposed. The existing production views, forms and decorators were inspected to explain behaviour, but the commit for this chapter changed only the test file.

**Filename / location before the code:** Project root PowerShell terminal; GitHub repository `mehreen-7547/ai-assisted-inventory-manager`

```powershell
git add accounts/tests.py
git commit -m "Add employee management and role permission tests"
git push origin main

git status --short
git log -1 --oneline
```

**Walkthrough:** `git add` stages the test file. `git commit` creates a saved revision, and `git push` updates the remote branch. The user reported the push was complete; GitHub was then queried independently. Its `main` branch points to commit `fc700cbf649f502aee6f7f407d42a89a4da13f17`, and the file contains the 12 new methods.

**Observed evidence and limits:** Verified GitHub commit `fc700cb` on `main` and confirmed the twelve methods in `accounts/tests.py`. The user's successful test output is the execution evidence; GitHub does not independently attest that the tests ran in CI.

**Navigation:** Previous: [Step 215](#step-215)

---

### Chapter summary

The committed file adds 12 tests for admin visibility, role restrictions, employee creation, email failure, editing, deactivation and password-setup email resend. The recorded full test run passed all 23 tests. Production application code was not changed in this commit. The next testing pass can cover unauthorized POST mutations, reactivation, invalid forms, and password-already-set resend behaviour.

**Recall question:** Why do we mock `send_welcome_email` when testing employee creation, and what does that mock *not* prove?
