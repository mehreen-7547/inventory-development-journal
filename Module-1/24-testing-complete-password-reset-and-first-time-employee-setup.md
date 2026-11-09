# Chapter 24 — Testing Complete Password Reset and First-Time Employee Password Setup

**Module:** 1 — Accounts  
**Journal steps:** 232–235  
**Verified code:** [accounts/tests.py at fc5361a](https://github.com/mehreen-7547/ai-assisted-inventory-manager/blob/fc5361a/accounts/tests.py)  
**Completion tag:** [m1-complete](https://github.com/mehreen-7547/ai-assisted-inventory-manager/releases/tag/m1-complete) (Git tag, not a release)

## Why this final chapter exists

The preceding chapter established that password-reset requests generated emails and that setup links contained valid tokens. Those checks did not prove that someone could actually follow the link and choose a password. Claude's approved v3 plan called for finishing Module 1 with **exactly two more tests**, then moving on to Supplier and Product management rather than expanding the accounts module.

<a id="step-232"></a>
### Step 232 — Confirm the repository is clean before changing tests

**Situation:** Before adding the final tests, an earlier extra blank line in `accounts/forms.py` had caused an uncommitted change. We needed to ensure no unrelated file would be included in the final commit.

**Filename / location:** Project root — PowerShell

```powershell
git status
# On branch main
# Your branch is up to date with 'origin/main'.
# nothing to commit, working tree clean
```

**Explanation:** A clean working tree means the next commit can be attributed to the two intended test additions. It does not prove the application is bug-free.

<a id="step-233"></a>
### Step 233 — Test a complete password-reset confirmation

**Situation:** An email link alone is not enough: the confirmation URL must accept a valid token and then save a new password. Django deliberately redirects the raw token URL to a `set-password` URL, keeping the token out of the final form URL.

**Filename / location:** `accounts/tests.py` — method in `PasswordWorkflowTests`

```python
    def test_password_reset_confirmation_changes_password(self):
        uid = urlsafe_base64_encode(force_bytes(self.user.pk))
        token = default_token_generator.make_token(self.user)

        reset_url = reverse(
            'password_reset_confirm',
            kwargs={'uidb64': uid, 'token': token}
        )

        response = self.client.get(reset_url)
        self.assertRedirects(
            response,
            reverse(
                'password_reset_confirm',
                kwargs={'uidb64': uid, 'token': 'set-password'}
            )
        )

        response = self.client.post(
            reverse(
                'password_reset_confirm',
                kwargs={'uidb64': uid, 'token': 'set-password'}
            ),
            {
                'new_password1': 'RecoveredPassword456!',
                'new_password2': 'RecoveredPassword456!'
            }
        )

        self.assertRedirects(
            response,
            reverse('password_reset_complete')
        )

        self.user.refresh_from_db()
        self.assertTrue(
            self.user.check_password('RecoveredPassword456!')
        )
        self.assertFalse(
            self.user.check_password('OldPassword123!')
        )
```

**Explanation:** `force_bytes` converts the user ID to bytes and `urlsafe_base64_encode` makes it safe for a URL. `default_token_generator` creates a token tied to the user. `reverse` builds the URL from the Django route name. The first request validates the token and redirects to the form URL. The POST submits matching new passwords. `refresh_from_db` reloads the saved user, and `check_password` verifies that the new password works and the old password no longer does. This is an isolated test using Django's test client; it does not send an external email.

<a id="step-234"></a>
### Step 234 — Test first-time password setup for a new employee

**Situation:** An administrator creates an employee without a usable password. That employee must be able to open the welcome/setup link and establish a first password before logging in normally.

**Filename / location:** `accounts/tests.py` — method in `PasswordWorkflowTests`

```python
    def test_new_employee_can_set_first_password(self):
        employee = CustomUser.objects.create_user(
            username='newsetupuser',
            email='newsetup@example.com',
            role='sales'
        )
        employee.set_unusable_password()
        employee.save()

        response = self.client.get(reverse('login'))
        setup_link = generate_setup_link(
            response.wsgi_request, employee
        )

        path = urlparse(setup_link).path
        response = self.client.get(path)

        self.assertEqual(response.status_code, 302)

        response = self.client.post(
            response.url,
            {
                'new_password1': 'FirstPassword456!',
                'new_password2': 'FirstPassword456!'
            }
        )

        self.assertRedirects(
            response,
            reverse('password_reset_complete')
        )

        employee.refresh_from_db()
        self.assertTrue(employee.has_usable_password())
        self.assertTrue(
            employee.check_password('FirstPassword456!')
        )
```

**Explanation:** `set_unusable_password` prevents a new employee from logging in with a known default password. `generate_setup_link` creates the initial-password link; `urlparse` extracts its path. The first GET should redirect to Django's password form, and the POST should finish at the reset-complete page. The final assertions confirm the account now has a usable password and accepts the chosen value. The test covers the setup flow, not actual SMTP delivery.

<a id="step-235"></a>
### Step 235 — Run the suite, publish the code and mark Module 1 complete

**Situation:** Both new tests must pass alongside the 45 existing tests before Module 1 can be closed. The Git tag provides a stable marker for the exact source revision.

**Filename / location:** Project root — PowerShell; `accounts/tests.py` on GitHub `main`

```powershell
python manage.py test
# Found 47 test(s).
# System check identified no issues (0 silenced).
# Ran 47 tests in 51.685s
# OK

git add accounts/tests.py
git commit -m "Complete Module 1 password recovery and account setup tests"
# [main fc5361a] ... 1 file changed, 85 insertions(+)

git push origin main
# 8f00b26..fc5361a main -> main

git status --short
# no output

git tag -a m1-complete -m "Module 1 complete - 47 tests passed"
git push origin m1-complete
# [new tag] m1-complete -> m1-complete

git log -1 --oneline
# fc5361a (HEAD -> main, tag: m1-complete, origin/main) Complete Module 1 password recovery and account setup tests
```

**Explanation:** Django creates and destroys an isolated test database, so this command does not reset the ordinary application database. `OK` confirms all 47 tests passed in the user's local environment. The commit makes the two new tests reviewable on GitHub; the annotated tag `m1-complete` marks that exact revision. The connected GitHub source independently confirms both new test methods and the pushed tag.

### Chapter result and limits

Module 1 is complete according to Claude's approved milestone. The 47 passing tests cover the chosen account workflows, including both password-completion paths. They do not prove live SMTP deliverability, production readiness, or every possible token edge case. No more account features are scheduled before the next business module.

**Next approved module:** Module 2 — Product & Supplier Management. Build Supplier management first, then Product management. Product category is a `CharField`, **not** a separate Category table; no opening stock field on the Product form. Stock changes belong to the later `inventory.change_stock()` and `StockMovement` workflow. Follow the original v3 plan for any further module ordering.

### Recall question

Why do the tests check the stored password after the HTTP redirect, and why does the employee start with an unusable password?
