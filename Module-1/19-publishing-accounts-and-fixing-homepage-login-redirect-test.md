# MODULE 1 — Employee accounts

## Chapter 19: Publishing the Completed Django Accounts Module to GitHub and Fixing the Home Page Login Redirect Test (Steps 195–203)

### Before we begin

Module 1 had working employee accounts, but the current implementation was not yet committed to the code repository. The older Module 0 tests also predated the new login protection.

### What we worked on

We protected the local files, made one baseline commit for completed work, checked the public repository for obvious credential risks and corrected a test that no longer matched the application's authentication rules.

### Picture it this way

The baseline commit is a photograph of the completed accounts module. Future commits record one new change at a time. The home-page test is like a visitor checking a locked office door: being redirected to reception is now the correct outcome, not a failure.

> **Study note:** These commands describe what was actually done. Do not repeat old commits, backups or pushes merely to follow this chapter.

### Jump to a step

- [Step 195: Preserve the working project before Git changes](#step-195)
- [Step 196: Choose one baseline commit instead of replaying old work](#step-196)
- [Step 197: Check that local secrets are excluded](#step-197)
- [Step 198: Stage and review the accounts implementation](#step-198)
- [Step 199: Inspect staged code for possible credentials](#step-199)
- [Step 200: Commit and publish the Module 1 baseline](#step-200)
- [Step 201: Discover why an old home-page test failed](#step-201)
- [Step 202: Test the intended login redirect](#step-202)
- [Step 203: Commit and push the corrected test](#step-203)

---

<a id="step-195"></a>
### Step 195: Preserve the working project before Git changes

**What was happening before this step?** The completed accounts module existed in the working directory, but some files were staged and others were not. Reorganizing Git history before protecting those files would have created an avoidable risk.

**What did we do, and what does it mean?** We copied the project to a sibling folder and confirmed `manage.py` was present. Then we compared file hashes, excluding `.git`, `.venv` and Python caches. This established a recoverable copy before any Git operations; it did not create a Git commit.

**Where was the code or command used?** PowerShell, from the Django project directory:

```powershell
Copy-Item -LiteralPath (Get-Location).Path -Destination "..\techmindz_inventory_module1_backup" -Recurse
Test-Path "..\techmindz_inventory_module1_backup\manage.py"
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** The user reported `True` for the file check and `Missing or different: 0` for the subsequent content comparison. This confirmed matching copies of the checked files, not a guarantee about excluded directories.

**Navigation:** Previous: [Step 194](18-shared-base-template-and-page-styling.md#step-194) · Next: [Step 196](#step-196)

---

<a id="step-196"></a>
### Step 196: Choose one baseline commit instead of replaying old work

**What was happening before this step?** We initially considered reconstructing each earlier Module 1 change as a separate Git commit. That would have added effort without improving the already completed implementation.

**What did we do, and what does it mean?** We changed the plan: keep the existing Module 0 commits, commit the finished Module 1 implementation once, and use small incremental commits only for future changes. We did not create the proposed `module1-snapshot` branch. The baseline would go onto the existing `main` branch.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git branch --show-current
git status --short
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** Git reported `main` and a mixture of added, modified and untracked account files. This was an inspection step; no files were altered.

**Navigation:** Previous: [Step 195](#step-195) · Next: [Step 197](#step-197)

---

<a id="step-197"></a>
### Step 197: Check that local secrets are excluded

**What was happening before this step?** The GitHub code repository was public. The project used a local `.env` file for credentials and a SQLite database that should not be uploaded with source code.

**What did we do, and what does it mean?** We asked Git to show ignored paths and check ignore rules. This matters because `git add` should collect application code, not email credentials or personal database records. The `.venv` path was also checked but did not appear in the reported output.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git status --ignored --short -- .env db.sqlite3 .venv
git check-ignore .env db.sqlite3 .venv
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** The observed output included `!! .env`, `!! db.sqlite3`, `.env` and `db.sqlite3`. Both files were ignored. The absence of `.venv` from that output did not prove its ignore status.

**Navigation:** Previous: [Step 196](#step-196) · Next: [Step 198](#step-198)

---

<a id="step-198"></a>
### Step 198: Stage and review the accounts implementation

**What was happening before this step?** The finished Module 1 files were spread across the `accounts` app, shared templates, core views and Django configuration. Some were already staged while others were still untracked.

**What did we do, and what does it mean?** We staged only the relevant source paths, then listed staged filenames and checked the diff. This was deliberately narrower than staging the entire repository. The staged list included migrations, forms, decorators, account pages and the shared base template.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git add accounts config/settings.py config/urls.py core/views.py core/templates/core/home.html templates
git diff --cached --name-only
git diff --cached --check
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** The full staged list contained 31 files. No error output from the whitespace check was reported. Staging changed the Git index, not the committed history.

**Navigation:** Previous: [Step 197](#step-197) · Next: [Step 199](#step-199)

---

<a id="step-199"></a>
### Step 199: Inspect staged code for possible credentials

**What was happening before this step?** Even with `.env` ignored, a developer could accidentally write a password directly inside a Python settings file. We therefore inspected staged source for common secret-related names before making a public commit.

**What did we do, and what does it mean?** The first suggested `git grep` syntax failed because `--cached` was placed incorrectly. We corrected the command and examined the actual matches. Environment-variable lookups are safer than embedding the credential values directly in committed source.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git -c core.pager=cat grep --cached -n -I -E 'SECRET_KEY|EMAIL_HOST_PASSWORD|PASSWORD[[:space:]]*=|API_KEY|PRIVATE_KEY' -- accounts config core templates
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** The corrected search found `SECRET_KEY = os.getenv("DJANGO_SECRET_KEY", "")` and `EMAIL_HOST_PASSWORD = os.getenv("EMAIL_HOST_PASSWORD", "")` in `config/settings.py`, plus the secret-key validation. No hardcoded credential appeared in these matches. This was a targeted search, not a comprehensive secret scan.

**Navigation:** Previous: [Step 198](#step-198) · Next: [Step 200](#step-200)

---

<a id="step-200"></a>
### Step 200: Commit and publish the Module 1 baseline

**What was happening before this step?** The account features were implemented, but GitHub still contained only the earlier Module 0 project history. The new work needed a stable starting point for future incremental development.

**What did we do, and what does it mean?** We created one commit for the entire finished accounts module and pushed it to `origin/main`. The baseline contains the account model, migrations, views, forms, URL routes, templates, styling and configuration changes. It is a snapshot, not a reconstructed sequence of how each feature was originally written.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git commit -m "Complete Module 1 accounts and authentication baseline"
git push origin main
git fetch origin main
git log -1 --format="%h %s" origin/main
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** Commit `96f54b1` contained 31 changed files with 1,277 insertions and 15 deletions. The user subsequently confirmed `origin/main` displayed that commit. GitHub commit: https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/96f54b1538516ef066125514b2a72ef75b04fced

**Navigation:** Previous: [Step 199](#step-199) · Next: [Step 201](#step-201)

---

<a id="step-201"></a>
### Step 201: Discover why an old home-page test failed

**What was happening before this step?** After the baseline was published, we ran the complete Django test suite. Four tests passed, but the old Module 0 home-page test expected an anonymous visitor to see the page immediately.

**What did we do, and what does it mean?** The test used `self.client.get(reverse("home"))` and expected HTTP 200. Module 1 had protected the home page with login requirements, so the actual response was HTTP 302. That is a redirect, not necessarily a broken page. We decided to update the outdated test rather than remove authentication.

**Where was the code or command used?** PowerShell, in the Django project directory:

```powershell
python manage.py test
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** The observed result was five tests, one failure: `test_home_page_is_available` expected `200` but received `302`. This failure identified a mismatch between the earlier test and the new access policy.

**Navigation:** Previous: [Step 200](#step-200) · Next: [Step 202](#step-202)

---

<a id="step-202"></a>
### Step 202: Test the intended login redirect

**What was happening before this step?** The old test still described the Module 0 public home page. A test for the current application should check where an unauthenticated visitor is sent, not demand that the protected page open.

**What did we do, and what does it mean?** In `core/tests.py`, we renamed the test and replaced the status-code assertion with `assertRedirects`. `reverse("login")` obtains the named login URL; the `next` query parameter preserves the destination so the user can return after signing in.

**Where was the code or command used?** File `core/tests.py`, inside class `ModuleZeroTests`; replace the old home-page test method with:

```python
def test_home_page_redirects_anonymous_users(self):
    response = self.client.get(reverse("home"))
    self.assertRedirects(
        response,
        f'{reverse("login")}?next={reverse("home")}'
    )
```

**How to read this change:** The method name describes the behaviour being checked. The test client sends a GET request without a logged-in user, and `assertRedirects` compares the resulting destination with the expected login URL.

**What was verified?** The focused test ran once and passed (`OK`). The full suite then ran five tests and passed. The redirect behaviour was verified; this did not by itself test successful login.

**Navigation:** Previous: [Step 201](#step-201) · Next: [Step 203](#step-203)

---

<a id="step-203"></a>
### Step 203: Commit and push the corrected test

**What was happening before this step?** The corrected test passed, but until it was committed the fix existed only in the local working directory. A separate commit would make the reason for the change easy to inspect later.

**What did we do, and what does it mean?** We staged only `core/tests.py`, created a focused commit and verified the working tree was clean. The test adjustment is separate from the large Module 1 baseline, demonstrating the incremental Git workflow we chose for new work.

**Where was the code or command used?** PowerShell, in the project repository:

```powershell
git add core/tests.py
git commit -m "Update home page test for login protection"
git log -1 --oneline
git status --short
git push origin main
```

**How to read this change:** The commands are shown in the order used for this part of the workflow. Read each line as a separate action; commands that inspect files do not create commits, whereas `git commit` and `git push` explicitly record or publish changes.

**What was verified?** Git showed commit `00b7735` with the expected message, followed by no status output. The push succeeded: `96f54b1..00b7735 main -> main`. GitHub commit: https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/00b7735a1ac19e88dc6bf091c036914dfb1f1387

**Navigation:** Previous: [Step 202](#step-202) · Next: Step 204 (authentication testing; chapter pending verification)

---

### Chapter summary

We protected the completed accounts implementation, published the Module 1 baseline as commit `96f54b1`, updated the anonymous home-page test to match login protection, and published that correction as commit `00b7735`. The observed Django test suite passed all five tests after the correction. The six new authentication tests belong to the next chapter and are not included as a published code commit here.

**Recall question:** Why should a protected home page be tested for a redirect rather than for HTTP 200 when no user is logged in?
