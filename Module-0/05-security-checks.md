# Module 0, Chapter 5: Security checks

## Step 37: Staging Security Verification Notes

#### Step 47 — Stage the updated journal

Assuming Step 46 completed successfully, run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal add inventory-development-journal.md
```

**What this does:** Stages the latest journal changes, including the security verification results, for the next Git commit.

This affects only your private journal repository.

Tell me **done** when the command finishes.

*Previous: Step 36 (Pushing Development Journal to GitHub)  |  Next: Step 38 (Committing Security Verification Notes)*

---

## Step 38: Committing Security Verification Notes

#### Step 48 — Commit the journal update

Assuming Step 47 completed successfully, run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record Git security verification"
```

**What this does:** Saves the staged security verification notes as a new commit in your local journal repository.

It does not upload the changes to GitHub yet.

Tell me **done** when the command finishes.

*Previous: Step 37 (Staging Security Verification Notes)  |  Next: Step 39 (Pushing Security Verification Notes)*

---

## Step 39: Pushing Security Verification Notes

#### Step 49 — Push the journal update to GitHub

Assuming Step 48 completed successfully, run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal push
```

**What this does:** Uploads your latest journal commit to your private GitHub repository.

**Expected result:** A line showing `main -> main`.

Tell me **done** when it finishes. We'll verify the upload before continuing.

#### How we checked it

**Step 49 completed.** I verified that your security verification notes have been pushed to your private GitHub repository.

#### Step 50 — Run Django's automated tests

Your project already contains five tests in `core/tests.py`. They check the homepage, timezone, SQLite connection, stock deduction, and transaction rollback.

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core
```

**What this does:**
- Creates a temporary test database.
- Runs the five tests.
- Reports whether each test passes.
- Removes the temporary test database afterward.

Your existing SQLite data and superuser account will remain unaffected.

**Expected result:** `Ran 5 tests` followed by `OK`.

If the Django server is still running, use a second VS Code terminal with the virtual environment activated.

Send me the output before we proceed.

*Previous: Step 38 (Committing Security Verification Notes)  |  Next: Step 40 (Understanding Temporary Test Models)*

---
