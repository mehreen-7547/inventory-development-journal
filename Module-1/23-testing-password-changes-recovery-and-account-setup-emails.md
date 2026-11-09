# Module 1 — Employee Accounts

## Chapter 23 — Testing Password Changes, Password Recovery and Account Setup Emails

**Journal steps 224–231 · Source commit `8f00b26`**

### Starting point

Chapter 22 left the inventory system with 35 passing tests. Password changes, password recovery, account-setup links and welcome emails already existed in the code but did not have dedicated workflow tests. We inspected the real views, URLs, helpers and settings before writing a ten-test batch.

### Practical scenario

An employee needs to change a password without losing the current session, a colleague forgets their password, and an administrator creates a new employee who must receive a setup link. At the same time, inactive or unknown accounts must not receive recovery emails. These are distinct paths with different expected behaviour.

### Chapter navigation

- [Step 224 — Inspect password-change, reset and email source files](#step-224)
- [Step 225 — Add test imports and isolate email delivery](#step-225)
- [Step 226 — Check password-change access and successful session preservation](#step-226)
- [Step 227 — Reject an incorrect current password](#step-227)
- [Step 228 — Test password-reset requests and unknown addresses](#step-228)
- [Step 229 — Prevent recovery emails for inactive accounts](#step-229)
- [Step 230 — Validate the account-setup token and welcome-email content](#step-230)
- [Step 231 — Run the full 45-test suite and verify the GitHub commit](#step-231)

---

<a id="step-224"></a>
### Step 224 — Inspect password-change, reset and email source files

**Scenario and reason:** The project already had a custom `changepassword` view, Django's built-in password-reset views, a setup-link helper and a welcome-email helper. Before adding tests, we checked which paths existed and what each was supposed to do. The important distinction is that this chapter tests existing implementation rather than inventing a new password feature.

**Filename / location:** `accounts/views.py`, `accounts/urls.py`, `accounts/utils.py`, `config/settings.py`

```python
@login_required(login_url='/login/')
def changepassword(request):
    if request.method == 'POST':
        form = PasswordChangeForm(request.user, request.POST)
        if form.is_valid():
            user = form.save()
            messages.success(request, 'Password changed successfully.')
            update_session_auth_hash(request, user)
            return redirect('home')
```

**Code walkthrough:** The login decorator rejects anonymous visitors. `PasswordChangeForm` validates the current and replacement passwords; `form.save()` stores the change. `update_session_auth_hash` preserves the authenticated session. URL configuration also provides reset request, done, confirmation and completion routes. `config/settings.py` defines a 24-hour password-reset timeout, though expiry was not separately tested in this batch.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous chapter, Step 223](22-testing-unauthorized-employee-changes-form-validation-and-reactivation.md#step-223) · [Next, Step 225](#step-225)

---

<a id="step-225"></a>
### Step 225 — Add test imports and isolate email delivery

**Scenario and reason:** Sending real email during a test would depend on network access and mail credentials. We imported Django's in-memory mail backend override so each test could inspect messages without contacting an SMTP server. We also imported token and URL helpers for validating the setup link. These imports belong with the existing imports at the top of the test file, while the new test class belongs at the bottom.

**Filename / location:** `accounts/tests.py` — imports and `PasswordWorkflowTests`

```python
from django.core import mail
from django.test import override_settings
from django.contrib.auth.tokens import default_token_generator
from django.utils.http import urlsafe_base64_decode
from django.utils.encoding import force_str
from urllib.parse import urlparse
from .utils import generate_setup_link, send_welcome_email

@override_settings(
    EMAIL_BACKEND='django.core.mail.backends.locmem.EmailBackend'
)
class PasswordWorkflowTests(TestCase):
    def setUp(self):
        self.user = CustomUser.objects.create_user(
            username='passworduser',
            email='passworduser@example.com',
            password='OldPassword123!', role='sales'
        )
```

**Code walkthrough:** `override_settings` changes only the test class's email backend. `mail.outbox` collects generated emails in memory. `setUp` creates a fresh sales account before every test, preventing state changes from leaking between tests. The imported token generator and decoding helpers are used to inspect the generated password-setup URL.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 224](#step-224) · [Next, Step 226](#step-226)

---

<a id="step-226"></a>
### Step 226 — Check password-change access and successful session preservation

**Scenario and reason:** A sales employee should be able to open the password-change form after login, but an anonymous visitor must be sent to login first. After a valid change, the new password must be stored without unexpectedly logging the employee out. We tested both page access and the session identifier after submission, not merely the HTTP response.

**Filename / location:** `accounts/tests.py` — password-change access and success tests

```python
def test_change_password_requires_login(self):
    response = self.client.get(reverse('change_password'))
    self.assertRedirects(
        response,
        reverse('login') + '?next=' + reverse('change_password')
    )

def test_change_password_page_loads(self):
    self.client.force_login(self.user)
    response = self.client.get(reverse('change_password'))
    self.assertEqual(response.status_code, 200)

def test_valid_password_change_keeps_session(self):
    self.client.force_login(self.user)
    response = self.client.post(reverse('change_password'), {
        'old_password': 'OldPassword123!',
        'new_password1': 'NewPassword456!',
        'new_password2': 'NewPassword456!'
    })
    self.assertRedirects(response, reverse('home'))
    self.user.refresh_from_db()
    self.assertTrue(self.user.check_password('NewPassword456!'))
    self.assertEqual(
        int(self.client.session['_auth_user_id']), self.user.pk
    )
```

**Code walkthrough:** `force_login` creates the authenticated test session. The POST keys match Django's `PasswordChangeForm`. `refresh_from_db` reloads the stored password hash before `check_password` verifies the replacement. The session check confirms that `update_session_auth_hash` kept this account logged in. The anonymous test checks the `next` redirect query parameter.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 225](#step-225) · [Next, Step 227](#step-227)

---

<a id="step-227"></a>
### Step 227 — Reject an incorrect current password

**Scenario and reason:** A user who knows only the new password they want to choose should not be able to replace the existing password without supplying the current one. This protects the password-change workflow from accidental or unauthorized updates within an active session. We submitted a wrong old password and verified the original password still works.

**Filename / location:** `accounts/tests.py` — invalid password-change test

```python
def test_wrong_old_password_is_rejected(self):
    self.client.force_login(self.user)
    response = self.client.post(reverse('change_password'), {
        'old_password': 'WrongPassword123!',
        'new_password1': 'NewPassword456!',
        'new_password2': 'NewPassword456!'
    })
    self.assertEqual(response.status_code, 200)
    self.user.refresh_from_db()
    self.assertTrue(self.user.check_password('OldPassword123!'))
```

**Code walkthrough:** The response status of 200 means the form was rendered again; it does not mean the password changed. `check_password` verifies the database still recognizes the original password. This test covers a wrong old password; mismatched new passwords and password-strength edge cases are not asserted here.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 226](#step-226) · [Next, Step 228](#step-228)

---

<a id="step-228"></a>
### Step 228 — Test password-reset requests and unknown addresses

**Scenario and reason:** Someone who forgets their password needs a reset page that sends a recovery link to a known active account. An unknown address should receive the same generic completion redirect but no email, avoiding disclosure of registered accounts. We tested the reset page, a valid email request and an unknown address using the in-memory mailbox.

**Filename / location:** `accounts/tests.py` — password-reset request tests

```python
def test_password_reset_page_loads(self):
    response = self.client.get(reverse('password_reset'))
    self.assertEqual(response.status_code, 200)

def test_password_reset_sends_email(self):
    response = self.client.post(reverse('password_reset'), {
        'email': self.user.email
    })
    self.assertRedirects(response, reverse('password_reset_done'))
    self.assertEqual(len(mail.outbox), 1)
    self.assertIn('password-reset/confirm/', mail.outbox[0].body)

def test_unknown_email_sends_nothing(self):
    response = self.client.post(reverse('password_reset'), {
        'email': 'unknown@example.com'
    })
    self.assertRedirects(response, reverse('password_reset_done'))
    self.assertEqual(len(mail.outbox), 0)
```

**Code walkthrough:** The Django password-reset view renders the page on GET and processes the submitted email on POST. A recognized account creates one mail message with a confirmation URL. An unknown address creates none, but still redirects to the done page. These assertions do not follow the link to complete a password reset.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 227](#step-227) · [Next, Step 229](#step-229)

---

<a id="step-229"></a>
### Step 229 — Prevent recovery emails for inactive accounts

**Scenario and reason:** Deactivation is meant to prevent a former employee from using the account. A password-reset email should therefore not be generated for an inactive account, even if its address exists in the database. We changed the test user's `is_active` flag and submitted a normal password-reset request, checking the mailbox afterward.

**Filename / location:** `accounts/tests.py` — inactive account reset test

```python
def test_inactive_user_gets_no_reset_email(self):
    self.user.is_active = False
    self.user.save()
    response = self.client.post(reverse('password_reset'), {
        'email': self.user.email
    })
    self.assertRedirects(response, reverse('password_reset_done'))
    self.assertEqual(len(mail.outbox), 0)
```

**Code walkthrough:** `is_active=False` is saved before the request. The generic done-page redirect avoids identifying account state in the response. An empty in-memory outbox proves no reset email was generated for this tested account. The test does not independently verify login rejection; that was covered in an earlier chapter.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 228](#step-228) · [Next, Step 230](#step-230)

---

<a id="step-230"></a>
### Step 230 — Validate the account-setup token and welcome-email content

**Scenario and reason:** New employees receive a temporary password-setup URL. A useful test should verify that the URL identifies the intended account and includes a token accepted by Django's token generator. We also need to ensure the welcome email addresses the correct recipient and actually contains the setup link, without sending external mail.

**Filename / location:** `accounts/tests.py` — setup-link and welcome-email tests

```python
def test_setup_link_contains_valid_token(self):
    response = self.client.get(reverse('login'))
    link = generate_setup_link(response.wsgi_request, self.user)
    parts = urlparse(link).path.strip('/').split('/')
    uid, token = parts[-2], parts[-1]
    self.assertEqual(
        force_str(urlsafe_base64_decode(uid)), str(self.user.pk)
    )
    self.assertTrue(
        default_token_generator.check_token(self.user, token)
    )

def test_welcome_email_contains_setup_link(self):
    response = self.client.get(reverse('login'))
    send_welcome_email(response.wsgi_request, self.user)
    self.assertEqual(len(mail.outbox), 1)
    self.assertEqual(mail.outbox[0].to, [self.user.email])
    self.assertIn(self.user.username, mail.outbox[0].body)
    self.assertIn('password-reset/confirm/', mail.outbox[0].body)
```

**Code walkthrough:** `generate_setup_link` encodes the user primary key and generates a password-reset token. `urlparse` extracts the path; the last two segments are decoded and checked. `send_welcome_email` renders the welcome template and sends through the in-memory backend. The assertions check one message, recipient, username and link. Token expiry and single-use completion were not separately exercised.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The corresponding production paths and helpers were inspected before this chapter was written.

**Navigation:** [Previous, Step 229](#step-229) · [Next, Step 231](#step-231)

---

<a id="step-231"></a>
### Step 231 — Run the full 45-test suite and verify the GitHub commit

**Scenario and reason:** The new tests must pass alongside the previously completed login, logout and employee-management tests. We ran the entire Django test suite locally rather than selecting only the new class, then committed and pushed the test file. The source commit is important because the journal should document code that reviewers can actually inspect. The terminal also revealed a separate uncommitted local change to `accounts/forms.py`, which must not be misrepresented as part of this commit.

**Filename / location:** Project root — PowerShell; GitHub `accounts/tests.py` on `main`

```powershell
python manage.py test
# Found 45 test(s).
# System check identified no issues (0 silenced).
# Ran 45 tests in 33.061s
# OK

git add accounts/tests.py
git commit -m "Add password change reset and welcome email tests"
git push origin main
git status --short
git log -1 --oneline
# 8f00b26 Add password change reset and welcome email tests
#  M accounts/forms.py
```

**Code walkthrough:** `python manage.py test` used a temporary test database and returned `OK` for all 45 tests. The commit added 128 lines to `accounts/tests.py` and pushed to `main`. GitHub resolves the branch to full SHA `8f00b260643ce8395f41f5667b5e046724cfc941`. The `M accounts/forms.py` line means the working tree was not completely clean; this file was not included in the commit and its local modifications remain unreviewed.

**Evidence:** The code shown is from the verified GitHub source and the tests passed in the user-supplied 45-test run. The local `accounts/forms.py` change is explicitly excluded from the published source commit.

**Navigation:** [Previous, Step 230](#step-230)

---

### Chapter result and limitations

The source repository contains ten password and email workflow tests. The user's local run discovered 45 tests and reported 45 passing in 33.061 seconds. The tests verify selected password-change, reset-request, setup-token and welcome-email behaviours. They do not verify a full reset-confirmation completion flow, expiry timing, real SMTP delivery or production deployment. A local uncommitted change to `accounts/forms.py` remains outside this commit.

### Recall question

Why is it necessary to check both the stored password and the session after a successful password change, and why is an in-memory email backend preferable for automated tests?
