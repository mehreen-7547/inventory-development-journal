# MODULE 0 — Getting started

## Chapter 5: Security checks (Steps 37–41)

### Before we begin

Before continuing to features, we checked that local secrets would not be uploaded and that the early testing work was clearly documented.

### What we worked on

These steps mostly record, save and explain security checks. They are not five separate changes to the application's permission system.

### Picture it this way

Imagine keeping a notebook of checks performed before accepting more changes. We should be able to say what was checked, and also what was not proved by those checks.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 37: Staging Security Verification Notes](#step-37)
- [Step 38: Committing Security Verification Notes](#step-38)
- [Step 39: Pushing Security Verification Notes](#step-39)
- [Step 40: Understand feasibility models](#step-40)
- [Step 41: Record and Push Test Results](#step-41)

---

<a id="step-37"></a>

### Step 37: Staging Security Verification Notes

**What was happening before this step?**

Before sharing project files, we checked that private settings and local database files were not being accidentally uploaded.

**What did we do, and what does it mean?**

A `.gitignore` file tells Git to ignore certain files, such as `.env`, when selecting new files. We noted these precautions so a future change would not accidentally publish a password or local data.

**Code or command from this step**

*1. Assuming Step 46 completed successfully, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How did we know what happened?**

The security findings were written into the journal; no new automated security test is created here.

**Where to go next:** Previous: [Step 36 — Pushing Development Journal to GitHub](04-server-and-admin-account.md#step-36) · Next: [Step 38 — Committing Security Verification Notes](05-security-checks.md#step-38)


---

<a id="step-38"></a>

### Step 38: Committing Security Verification Notes

**What was happening before this step?**

The security-check notes were complete, so we recorded them as a journal commit.

**What did we do, and what does it mean?**

This created a history entry about the checks. It did not, on its own, perform another security test.

**Code or command from this step**

*1. Assuming Step 47 completed successfully, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Git security verification"
```

**How did we know what happened?**

No code test occurs during the commit itself.

**Where to go next:** Previous: [Step 37 — Staging Security Verification Notes](05-security-checks.md#step-37) · Next: [Step 39 — Pushing Security Verification Notes](05-security-checks.md#step-39)


---

<a id="step-39"></a>

### Step 39: Pushing Security Verification Notes

**What was happening before this step?**

The latest security notes still needed to be copied to GitHub.

**What did we do, and what does it mean?**

A push uploaded this journal update. The database and secret files were meant to stay local.

**Code or command from this step**

*1. Assuming Step 48 completed successfully, run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How did we know what happened?**

The following note indicates the published security entry was part of the journal history.

**Where to go next:** Previous: [Step 38 — Committing Security Verification Notes](05-security-checks.md#step-38) · Next: [Step 40 — Understand feasibility models](05-security-checks.md#step-40)


---

<a id="step-40"></a>

### Step 40: Understand feasibility models

**What was happening before this step?**

The inventory project would eventually predict future demand. Before attempting that feature, we needed to see whether the laptop could run the model tools.

**What did we do, and what does it mean?**

The test models mentioned here were experiments, not the final prediction feature. Their purpose was to learn what software and computer resources were available before building a forecasting screen or making accuracy claims.

**Code or command from this step**

*1. Inside core/tests.py, we have code similar to.*

```python
class ExampleProduct(models.Model):
    on_hand = models.PositiveIntegerField(default=0)
```

**How did we know what happened?**

Our notes distinguishes feasibility testing from an implemented inventory forecasting module.

**Where to go next:** Previous: [Step 39 — Pushing Security Verification Notes](05-security-checks.md#step-39) · Next: [Step 41 — Record and Push Test Results](05-security-checks.md#step-41)


---

<a id="step-41"></a>

### Step 41: Record and Push Test Results

**What was happening before this step?**

We had early experiment results and wanted a written record of what worked and what did not.

**What did we do, and what does it mean?**

Documenting the result prevents us from forgetting the conditions of the test. This step saved and uploaded notes; it did not make a forecasting model available inside the Django website.

**Code or command from this step**

*1. Run this in your VS Code PowerShell terminal.*

```powershell
Add-Content -Path "C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

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

*2. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

*3. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Module 0 automated test results"
```

*4. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How did we know what happened?**

The historical note provides results but does not reproduce a complete independent benchmark here.

**Where to go next:** Previous: [Step 40 — Understand feasibility models](05-security-checks.md#step-40) · Next: [Step 42 — Installing PyTorch](06-ai-forecasting-feasibility-test.md#step-42)

---

### The point of this chapter

Imagine keeping a notebook of checks performed before accepting more changes. We should be able to say what was checked, and also what was not proved by those checks.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

