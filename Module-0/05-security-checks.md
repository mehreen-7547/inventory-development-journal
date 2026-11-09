# MODULE 0 — Foundations

## Chapter 5: Security checks (Steps 37–41)

### Before we begin

Before continuing to features, we checked that local secrets would not be uploaded and that the early testing work was clearly documented.

### What we worked on

These steps mostly record, save and explain security checks. They are not five separate changes to the application's permission system.

### Picture it this way

Imagine keeping a notebook of checks performed before accepting more changes. We should be able to say what was checked, and also what was not proved by those checks.

### Find a step

- [Step 37: Staging Security Verification Notes](#step-37)
- [Step 38: Committing Security Verification Notes](#step-38)
- [Step 39: Pushing Security Verification Notes](#step-39)
- [Step 40: Understand feasibility models](#step-40)
- [Step 41: Record and Push Test Results](#step-41)

---

<a id="step-37"></a>

### Step 37: Staging Security Verification Notes

#### Before this step

Before sharing project files, we checked that private settings and local database files were not being accidentally uploaded. The preceding step, Step 36, covered pushing development journal to github, so this was the next part of the development sequence.

#### What we did and why

A .gitignore file tells Git to ignore certain files, such as .env, when selecting new files. We noted these precautions so a future change would not accidentally publish a password or local data.

#### Code and commands

**Step 37, block 1.** This code sample or terminal command was used while working on staging security verification notes. The content below remains in its original sequence for Step 37, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 37, staging security verification notes involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 37. For Step 37, the proposed way to check staging security verification notes was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 38 before a complete result could be claimed.

#### Next

[Step 36: Pushing Development Journal to GitHub](../Module-0/04-server-and-admin-account.md#step-36) · [Step 38: Committing Security Verification Notes](#step-38)


---

<a id="step-38"></a>

### Step 38: Committing Security Verification Notes

#### Before this step

The security-check notes were complete, so we saved them as a journal commit. Immediately before this, Step 37 dealt with staging security verification notes; the work here continued the same part of the application.

#### What we did and why

This created a history entry about the checks. It did not, on its own, perform another security test.

#### Code and commands

**Step 38, block 1.** This code sample or terminal command was used while working on committing security verification notes. The content below remains in its original sequence for Step 38, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Git security verification"
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 38, committing security verification notes involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 38. For Step 38, the proposed way to check committing security verification notes was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 39 before a complete result could be claimed.

#### Next

[Step 37: Staging Security Verification Notes](#step-37) · [Step 39: Pushing Security Verification Notes](#step-39)


---

<a id="step-39"></a>

### Step 39: Pushing Security Verification Notes

#### Before this step

The latest security notes still needed to be copied to GitHub. Immediately before this, Step 38 dealt with committing security verification notes; the work here continued the same part of the application.

#### What we did and why

A push uploaded this journal update. The database and secret files were meant to stay local.

#### Code and commands

**Step 39, block 1.** This code sample or terminal command was used while working on pushing security verification notes. The content below remains in its original sequence for Step 39, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push
```

**Step 39, block 2.** This code sample or terminal command was used while working on pushing security verification notes. The content below remains in its original sequence for Step 39, block 2.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 39, pushing security verification notes involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 39, pushing security verification notes involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 39. For Step 39, the proposed way to check pushing security verification notes was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 40 before a complete result could be claimed.

#### Next

[Step 38: Committing Security Verification Notes](#step-38) · [Step 40: Understand feasibility models](#step-40)


---

<a id="step-40"></a>

### Step 40: Understand feasibility models

#### Before this step

The inventory project would eventually predict future demand. Before attempting that feature, we needed to see whether the laptop could run the model tools. Immediately before this, Step 39 dealt with pushing security verification notes; the work here continued the same part of the application.

#### What we did and why

The test models mentioned here were experiments, not the final prediction feature. Their purpose was to learn what software and computer resources were available before building a forecasting screen or making accuracy claims.

#### Code and commands

**Step 40, block 1.** This code sample or terminal command was used while working on understand feasibility models. The content below remains in its original sequence for Step 40, block 1.

```python
class ExampleProduct(models.Model):
    on_hand = models.PositiveIntegerField(default=0)
```

**Step 40, block 2.** This code sample or terminal command was used while working on understand feasibility models. The content below remains in its original sequence for Step 40, block 2.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core
```

#### How the code works

**Block 1 (python, 2 lines).** In Step 40, understand feasibility models involved `core/tests.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `class ExampleProduct(models.Model):` declares a Python class, grouping related data or behaviour together. `on_hand = models.PositiveIntegerField(default=0)` sets the named value used by the next operation in this file.

**Block 2 (powershell, 1 lines).** In Step 40, understand feasibility models involved `core/tests.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core` starts Python or one of its tools from the development terminal.

#### How we checked it

For Step 40 (Understand feasibility models), we compared the visible terminal output with the requested action. The visible result for Step 40 was described as “your output confirms”; this only addressed the understand feasibility models check in `core/tests.py`.

#### Next

[Step 39: Pushing Security Verification Notes](#step-39) · [Step 41: Record and Push Test Results](#step-41)


---

<a id="step-41"></a>

### Step 41: Record and Push Test Results

#### Before this step

We had early experiment results and wanted a written record of what worked and what did not. Immediately before this, Step 40 dealt with understand feasibility models; the work here continued the same part of the application.

#### What we did and why

Documenting the result prevents us from forgetting the conditions of the test. This step saved and uploaded notes; it did not make a forecasting model available inside the Django website.

#### Code and commands

**Step 41, block 1.** This code sample or terminal command was used while working on record and push test results. The content below remains in its original sequence for Step 41, block 1.

```powershell
Add-Content -Path "C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

## Module 0 — Django Automated Testing

### Objective
Verify that the initial Django setup and proposed SQLite stock-update approach work correctly.

### Command
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core

### Command Explanation
- python: Executes Python.
- .\: Refers to the current directory.
- manage.py: Django's management script.
- test: Runs automated tests.
- core: Limits testing to the core Django app.

### Tests Performed
1. Homepage returns HTTP 200.
2. Django uses the Asia/Kolkata timezone.
3. SQLite database connection works.
4. Conditional stock deduction refuses a second sale when stock is exhausted.
5. A failed database transaction rolls back its stock deduction.

### Actual Results
Found 5 test(s).
System check identified no issues (0 silenced).
Ran 5 tests in 0.084s
OK

### Important Observations
- No permanent inventory models or tables have been created yet.
- Tests 4 and 5 use temporary ExampleProduct models and database tables.
- Django creates and destroys a separate test database.
- The existing development database and superuser remain unaffected.
- The stock test is sequential; it does not establish simultaneous-request safety.

### Errors
None.

### Status
PASS — All five Module 0 tests completed successfully.
'@
```

**Step 41, block 2.** This code sample or terminal command was used while working on record and push test results. The content below remains in its original sequence for Step 41, block 2.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**Step 41, block 3.** This code sample or terminal command was used while working on record and push test results. The content below remains in its original sequence for Step 41, block 3.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Module 0 automated test results"
```

**Step 41, block 4.** This code sample or terminal command was used while working on record and push test results. The content below remains in its original sequence for Step 41, block 4.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push
```

#### How the code works

**Block 1 (powershell, 43 lines).** In Step 41, record and push test results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The comment `## Module 0 — Django Automated Testing` explains the intention of the following section; it does not execute as application logic. The comment `### Objective` explains the intention of the following section; it does not execute as application logic.

**Lines 8–14:** The comment `### Command` explains the intention of the following section; it does not execute as application logic. The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core` starts Python or one of its tools from the development terminal. The comment `### Command Explanation` explains the intention of the following section; it does not execute as application logic.

**Lines 15–21:** The comment `### Tests Performed` explains the intention of the following section; it does not execute as application logic.

**Lines 22–28:** The comment `### Actual Results` explains the intention of the following section; it does not execute as application logic.

**Lines 29–35:** The comment `### Important Observations` explains the intention of the following section; it does not execute as application logic.

**Lines 36–42:** The comment `### Errors` explains the intention of the following section; it does not execute as application logic. The comment `### Status` explains the intention of the following section; it does not execute as application logic.

**Lines 43–43:** The lines starting with `'@` supply the values or page content for this part of inventory-development-journal.md. These lines are kept together because their order and indentation matter.

**Block 2 (powershell, 1 lines).** In Step 41, record and push test results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (powershell, 1 lines).** In Step 41, record and push test results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 4 (powershell, 1 lines).** In Step 41, record and push test results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 41. For Step 41, the proposed way to check record and push test results was to run `git status` and inspect the local or remote history for the expected change. Step 41 did not establish a successful outcome for record and push test results.

#### Next

[Step 40: Understand feasibility models](#step-40) · [Step 42: Installing PyTorch](../Module-0/06-ai-forecasting-feasibility-test.md#step-42)


### Check your understanding

Without looking at the code, explain why **security checks** was needed at this point, and describe what the application did differently after the final step of this chapter.
