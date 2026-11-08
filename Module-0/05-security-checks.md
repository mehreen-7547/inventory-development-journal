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

## Step 40: Understanding Temporary Test Models

You're correct. **We haven't created any actual inventory models or tables yet.**

I checked your `core/tests.py`. Tests 4 and 5 use a **temporary `ExampleProduct` model** defined inside the test code.

#### Test 4 — Preventing stock from becoming negative

The test temporarily creates a product with one unit in stock.

    **Initial stock**
    1

    **Final stock**
    0

This tests whether our proposed stock deduction method refuses a sale when insufficient stock remains.

**Limitation:** The two sales are attempted sequentially. This does not yet prove safety under genuinely simultaneous requests.

#### Test 5 — Rolling back a failed transaction

Imagine a sale starts processing:

1. Product stock starts at 2.
2. The database deducts 1 unit.
3. Something fails before the sale is completed.
4. The database must undo the deduction.

The test verifies that the stock returns to **2**, rather than incorrectly remaining at 1.

#### Where does the temporary model come from?

Inside `core/tests.py`, we have code similar to:

```python
class ExampleProduct(models.Model):
    on_hand = models.PositiveIntegerField(default=0)
```

The test then creates a temporary database table using Django's `schema_editor()` and deletes it afterward.

| | Temporary test model | Actual inventory model |
|---|---|---|
| Created? | During testing | Not yet |
| Stores real business data? | No | Eventually |
| Persists after testing? | No | Yes |
| Purpose | Verify database behavior | Run the inventory system |

**Why are we testing this now?**

We're checking whether SQLite supports the basic stock-update and rollback behavior we plan to use before implementing the actual inventory workflow.

These are preliminary technical checks, not tests of a completed inventory system.

**Current position:** Step 50 — Running the five Module 0 tests.

#### How we checked it

**All five tests passed.** Your output confirms that Django successfully ran the tests without errors.

#### Breaking down the command

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core
```

| Part | Meaning |
|---|---|
| `python` | Runs Python. |
| `.\` | Means start from the current terminal directory. |
| `techmindz_inventory_module0_sqlite_atomic\` | The folder containing your Django project. |
| `manage.py` | Django's command-line management script. |
| `test` | Tells Django to run automated tests. |
| `core` | Specifies which Django app to test. |

#### What happens internally?

    **Python executes `manage.py`**

    **Django receives `test core`**
    Find tests belonging to the core app

    **Creates a temporary test database**

    **Executes five tests**
    ..... = five successful tests

    **Deletes the test database**
    OK — all tests passed

#### Why specify `core`?

Compare:

- `python manage.py test` — discovers and runs tests across the Django project.
- `python manage.py test core` — runs only tests belonging to the `core` app.

We use the longer command because your terminal is currently in the **outer folder**, whereas `manage.py` is inside the inner Django project folder.

**Next:** We'll record the successful test results in your private development journal before continuing Module 0.

*Previous: Step 39 (Pushing Security Verification Notes)  |  Next: Step 41 (Record and Push Test Results)*

---
