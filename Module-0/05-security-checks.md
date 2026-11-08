# MODULE 0 — Foundations

## Chapter 5: Security checks (Steps 37–41)

**Where we are.** Before adding application users, we reviewed what should and should not enter version control, and recorded the setup checks.

**What this chapter gives us.** Differentiate documentation/Git operations from actual security tests.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why .env, database files, and credentials must not be committed.

### Find a step

- [Step 37: Staging Security Verification Notes](#step-37)
- [Step 38: Committing Security Verification Notes](#step-38)
- [Step 39: Pushing Security Verification Notes](#step-39)
- [Step 40: Understand feasibility models](#step-40)
- [Step 41: Record and Push Test Results](#step-41)

---

<a id="step-37"></a>

### Step 37: Staging Security Verification Notes

**Why we are doing this.** We recorded the repository-security checks so future readers could see why local secrets were not committed.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 46 completed successfully, run.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**How it works.** Staging the security note prepares a documentation revision. It does not modify the `.gitignore` policy by itself.

**How we checked it.** The security findings were written into the journal; no new automated security test is created here.

**Connects to.** Previous: [Step 36 — Pushing Development Journal to GitHub](04-server-and-admin-account.md#step-36) · Next: [Step 38 — Committing Security Verification Notes](05-security-checks.md#step-38)


---

<a id="step-38"></a>

### Step 38: Committing Security Verification Notes

**Why we are doing this.** The security verification needed its own local commit for traceability.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 47 completed successfully, run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Git security verification"
```

**How it works.** `git commit` saves the staged security notes locally and gives the milestone a searchable label.

**How we checked it.** No code test occurs during the commit itself.

**Connects to.** Previous: [Step 37 — Staging Security Verification Notes](05-security-checks.md#step-37) · Next: [Step 39 — Pushing Security Verification Notes](05-security-checks.md#step-39)


---

<a id="step-39"></a>

### Step 39: Pushing Security Verification Notes

**Why we are doing this.** The security notes were published so the private journal had the latest record.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Assuming Step 48 completed successfully, run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How it works.** `git push` synchronizes local journal history with its remote. The application repository remains separate.

**How we checked it.** The following note indicates the published security entry was part of the journal history.

**Connects to.** Previous: [Step 38 — Committing Security Verification Notes](05-security-checks.md#step-38) · Next: [Step 40 — Understand feasibility models](05-security-checks.md#step-40)


---

<a id="step-40"></a>

### Step 40: Understand feasibility models

**Why we are doing this.** Before trying time-series forecasting, we clarified that test models were temporary feasibility checks, not the finished AI inventory feature.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Inside core/tests.py, we have code similar to.*

```python
class ExampleProduct(models.Model):
    on_hand = models.PositiveIntegerField(default=0)
```

**How it works.** The experimental scripts measure whether chosen models run under available computing resources. Results must be understood as environment tests, not production forecasting accuracy.

**How we checked it.** The source distinguishes feasibility testing from an implemented inventory forecasting module.

**Connects to.** Previous: [Step 39 — Pushing Security Verification Notes](05-security-checks.md#step-39) · Next: [Step 41 — Record and Push Test Results](05-security-checks.md#step-41)


---

<a id="step-41"></a>

### Step 41: Record and Push Test Results

**Why we are doing this.** We needed to preserve early feasibility findings in the development record and publish them alongside the setup notes.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in your VS Code PowerShell terminal.*

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

*2. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

*3. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Module 0 automated test results"
```

*4. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How it works.** The example commands/text record test observations and then commit/push the journal update. They document experiments rather than adding a live AI endpoint.

**How we checked it.** The historical note provides results but does not reproduce a complete independent benchmark here.

**Connects to.** Previous: [Step 40 — Understand feasibility models](05-security-checks.md#step-40) · Next: [Step 42 — Installing PyTorch](06-ai-forecasting-feasibility-test.md#step-42)

---

**Chapter recap.** Differentiate documentation/Git operations from actual security tests.

**Practice explaining:** Explain why .env, database files, and credentials must not be committed.

