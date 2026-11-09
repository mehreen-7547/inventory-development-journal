# MODULE 1 — Employee accounts

## Chapter 20: Testing Django Login, Logout, Inactive Accounts and Authentication Sessions (Steps 204–209)

### Before we begin

The accounts module could log employees in and out, but the Accounts test file still contained Django's default placeholder. The five existing tests belonged to the Core app and did not verify these authentication paths.

### What we worked on

We wrote six focused tests for loading the login page, signing in, denying incorrect or inactive accounts, rejecting GET logout requests, and clearing an authenticated session. Then we ran the full suite and published the test file in a separate Git commit.

### Picture it this way

Think of an employee arriving at a workplace. A valid badge opens the door; an incorrect or disabled badge does not. Leaving through the correct exit ends access. The tests simulate those situations using Django's test client and check the results automatically.

> **Study note:** The test credentials are illustrative data in an isolated test database. Do not create real accounts with this example password. The test code was verified against the actual committed file and GitHub diff.

### Jump to a step

- [Step 204: Create a repeatable test employee](#step-204)
- [Step 205: Check the login page and a successful login](#step-205)
- [Step 206: Reject wrong passwords and inactive employees](#step-206)
- [Step 207: Require POST for logout and clear the session](#step-207)
- [Step 208: Run all eleven Django tests together](#step-208)
- [Step 209: Commit and push the authentication tests](#step-209)

---

<a id="step-204"></a>
### Step 204: Create a repeatable test employee

**What was happening before this step?** The accounts app already handled login and logout, but its `accounts/tests.py` file contained only Django's default placeholder. We needed a repeatable employee account that would not depend on anyone's actual database records.

**What did we do, and what does it mean?** We introduced a `TestCase` class and a `setUp()` method. Django calls `setUp()` before each test, so every test gets its own employee with known credentials. `create_user()` is the custom-user manager's method and stores the password in Django's normal hashed form. This user is test data, not an account created in the real application.

**Filename / location before the code:** `accounts/tests.py`, inside `AuthenticationTests`.

```python
from django.test import TestCase
from django.urls import reverse
from .models import CustomUser


class AuthenticationTests(TestCase):
    def setUp(self):
        self.user = CustomUser.objects.create_user(
            username='testemployee',
            email='testemployee@example.com',
            password='TestPassword123!',
            role='sales'
        )
```

**Line-by-line understanding:** `TestCase` supplies an isolated test database and a browser-like `self.client`. `reverse` finds a URL by its Django name. `CustomUser` is the project's user model. `setUp` prepares the same starting account before each individual assertion.

**Test result and evidence:** The complete test run later discovered 11 tests and passed, including the six tests in this class. There was no separately recorded run of only this setup method.

**Navigation:** Previous: [Step 203](19-publishing-accounts-and-fixing-homepage-login-redirect-test.md#step-203) · Next: [Step 205](#step-205)

---

<a id="step-205"></a>
### Step 205: Check the login page and a successful login

**What was happening before this step?** A page can exist without actually accepting credentials. We therefore needed to check both the first visit to `/login/` and what happens after the employee submits the correct username and password.

**What did we do, and what does it mean?** One test sends a GET request and checks HTTP 200, meaning the login page rendered. Another sends a POST request with valid credentials, checks the redirect to the named home page, and confirms the user ID was stored in the session. This verifies the behaviour of the existing `loginpage` view; it does not change production login code.

**Filename / location before the code:** `accounts/tests.py`, inside `AuthenticationTests`.

```python
def test_login_page_loads(self):
    response = self.client.get(reverse('login'))
    self.assertEqual(response.status_code, 200)

def test_valid_login(self):
    response = self.client.post(reverse('login'), {
        'username': 'testemployee',
        'password': 'TestPassword123!'
    })
    self.assertRedirects(response, reverse('home'))
    self.assertEqual(
        int(self.client.session['_auth_user_id']),
        self.user.pk
    )
```

**Line-by-line understanding:** `get()` simulates opening the page. `post()` simulates submitting the login form. `assertRedirects` checks the target, while `_auth_user_id` is Django's session entry for the signed-in user. `self.user.pk` is the ID of the test employee created in `setUp`.

**Test result and evidence:** The user ran `python manage.py test`; the suite reported 11 tests and `OK`. Both tests passed as part of that run.

**Navigation:** Previous: [Step 204](#step-204) · Next: [Step 206](#step-206)

---

<a id="step-206"></a>
### Step 206: Reject wrong passwords and inactive employees

**What was happening before this step?** A login system must not create a signed-in session for someone who supplies the wrong password. It must also reject an account that an administrator has deactivated, even if the old password is correct.

**What did we do, and what does it mean?** We added two negative-path tests. The wrong-password test submits invalid credentials. The inactive-account test first sets `is_active` to `False`, saves the employee, and submits the otherwise correct credentials. Both assert that the response remains HTTP 200 on the login page and that no authentication user ID was placed in the session.

**Filename / location before the code:** `accounts/tests.py`, inside `AuthenticationTests`.

```python
def test_invalid_login(self):
    response = self.client.post(reverse('login'), {
        'username': 'testemployee',
        'password': 'WrongPassword123!'
    })
    self.assertEqual(response.status_code, 200)
    self.assertNotIn('_auth_user_id', self.client.session)

def test_inactive_user_cannot_login(self):
    self.user.is_active = False
    self.user.save()
    response = self.client.post(reverse('login'), {
        'username': 'testemployee',
        'password': 'TestPassword123!'
    })
    self.assertEqual(response.status_code, 200)
    self.assertNotIn('_auth_user_id', self.client.session)
```

**Line-by-line understanding:** `is_active` is inherited from Django's user model. `save()` persists the deactivation in the test database. `assertNotIn` is the important security check: merely displaying the login page is insufficient if the session was accidentally authenticated.

**Test result and evidence:** Both tests passed in the recorded 11-test run. They verify these two login denial cases; they do not establish a complete account lockout or brute-force protection policy.

**Navigation:** Previous: [Step 205](#step-205) · Next: [Step 207](#step-207)

---

<a id="step-207"></a>
### Step 207: Require POST for logout and clear the session

**What was happening before this step?** A logout link that accepts a simple GET request can log a user out just by opening a URL. The existing `logoutpage` view uses Django's `@require_POST` decorator, so we needed to verify that restriction and successful logout.

**What did we do, and what does it mean?** The first test calls the logout URL with GET and expects HTTP 405, meaning the method is not allowed. The second signs in the test employee with `force_login`, submits a POST to logout, checks the redirect to login and confirms the session no longer contains the authentication user ID.

**Filename / location before the code:** `accounts/tests.py`, inside `AuthenticationTests`.

```python
def test_logout_requires_post(self):
    response = self.client.get(reverse('logout'))
    self.assertEqual(response.status_code, 405)

def test_logout_clears_session(self):
    self.client.force_login(self.user)
    response = self.client.post(reverse('logout'))
    self.assertRedirects(response, reverse('login'))
    self.assertNotIn('_auth_user_id', self.client.session)
```

**Line-by-line understanding:** `force_login()` creates an authenticated test session without submitting the login form again; this keeps the logout test focused. HTTP 405 confirms the GET request is rejected. `assertNotIn` verifies that logout removed the session's logged-in user identifier.

**Test result and evidence:** Both logout tests passed in the recorded complete test run. These tests check method restriction and session removal, not every browser or cross-site request scenario.

**Navigation:** Previous: [Step 206](#step-206) · Next: [Step 208](#step-208)

---

<a id="step-208"></a>
### Step 208: Run all eleven Django tests together

**What was happening before this step?** Adding six accounts tests could have broken older checks in `core/tests.py`. A passing individual authentication test would not show whether the whole project still behaved as expected.

**What did we do, and what does it mean?** We ran Django's standard test command from the project root. Django discovered five existing Core tests and six new Accounts tests. The test runner created a temporary test database, performed Django system checks, executed all eleven tests and destroyed the test database afterward. The application's real SQLite data was not the target of these tests.

**Filename / location before the code:** Project root terminal.

```powershell
python manage.py test
```

**Line-by-line understanding:** `manage.py` loads the project's Django settings. `test` finds `TestCase` methods whose names begin with `test_`. Each dot in the console output represents a completed passing test. `OK` means the suite completed without reported failures or errors.

**Test result and evidence:** The user supplied the full console output: `Found 11 test(s).`, `System check identified no issues (0 silenced).`, eleven dots, `Ran 11 tests in 3.235s`, and `OK`. No test failures were reported.

**Navigation:** Previous: [Step 207](#step-207) · Next: [Step 209](#step-209)

---

<a id="step-209"></a>
### Step 209: Commit and push the authentication tests

**What was happening before this step?** The tests were passing locally, but they still needed to be saved in the code repository so later work could build on a known, inspectable checkpoint.

**What did we do, and what does it mean?** We staged only `accounts/tests.py`, committed the new test class, and pushed to the existing `main` branch. The user then ran `git status --short` and `git log -1 --oneline`. The empty status output indicated a clean working tree, and the log showed the local and remote tracking references at the same commit.

**Filename / location before the code:** PowerShell terminal opened in the Git repository.

```powershell
git add accounts/tests.py
git commit -m "Add authentication login and logout tests"
git push origin main

git status --short
git log -1 --oneline
```

**Line-by-line understanding:** `git add` selects the file, `git commit` records it locally and `git push` sends it to GitHub. The final status command prints changed files if any remain. `HEAD -> main, origin/main` on the log line means the current local branch and the remote-tracking branch point to the same recorded commit.

**Test result and evidence:** The observed final output was `23c5de4 (HEAD -> main, origin/main) Add authentication login and logout tests`, with no lines from `git status --short`. GitHub's commit diff independently shows six new tests in `accounts/tests.py`: https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/23c5de489e21850b63326e724a809cfb2fdee543

**Navigation:** Previous: [Step 208](#step-208)

---

### Chapter summary

Six authentication tests were added to `accounts/tests.py`. The full suite of 11 tests passed, and commit `23c5de4` is present on `main` and `origin/main`. No production authentication view, form, model or URL was changed by this commit.

**Recall question:** Why is checking that `_auth_user_id` is absent from the session stronger than checking only that a failed login returned HTTP 200?
