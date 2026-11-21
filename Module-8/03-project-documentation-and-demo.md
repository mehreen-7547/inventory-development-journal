# Module 8 — Chapter 3: Project README and five-minute demo (Steps 533–535)

## Chapter introduction

Chapters 1 and 2 completed the Manager attention panel and historical forecast comparison. This chapter documents how to install, explain and demonstrate the implemented application without pretending that pending UI refinement or final submission checks are complete. **No Django application code was changed in this chapter.**

**Source commits:** [7b18ee9](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/7b18ee9) (main README) and [951e5be](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/951e5be) (demo script).

## Step 533 — Inspect the implemented project before documenting it

**Before this step.** The repository had `README_M0.md` for initial feasibility but no root `README.md` for the completed system. We needed documentation grounded in the actual implementation.

**What we did.** Inspected `config/urls.py`, app URL modules, `accounts/models.py`, `core/views.py`, `config/settings.py`, `requirements.txt`, `requirements-ai.txt`, `.env.example`, and the historical evaluation service. Confirmed three account roles, local SQLite configuration, role-specific dashboards, operational routes, and the Manager-only evaluation route.

**File: CLASS/EXISTING** `accounts/models.py`. **Code (exact excerpt):**

```python
ROLE_CHOICES = (
    ('admin', 'Admin'),
    ('manager', 'Inventory Manager'),
    ('sales', 'Sales Staff'),
)
```

**File: CLASS/EXISTING** `config/urls.py`. **Code (exact excerpt):**

```python
path('', include('intelligence.urls')),
```

**Walkthrough.** The user model defines which roles can sign in. The project URL configuration includes the intelligence routes, including the eight-week comparison. Documentation must describe these actual routes and avoid inventing unimplemented pages.

**How we checked it.** Read the connected GitHub source at `main`; the expected files and routes were present. This is a source inspection, not a fresh local Django test run.

**Next:** Step 534.

## Step 534 — Create the main project README

**Before this step.** `README_M0.md` covered only Module 0 setup, so a visitor to the repository lacked an overview of the implemented application.

**What we did.** Created a root README describing accounts, suppliers, products, stock, sales, purchasing, dashboards, AI decision support, the historical evaluation methodology, local installation, limitations and test instructions. Retained the original Module 0 documentation.

**File: NEW** `README.md`. **Code (exact Markdown excerpt):**

```markdown
## Implemented features

- **Accounts and roles:** Admin, Inventory Manager and Sales Staff; login/logout, password changes and recovery, employee management.
- **Catalog:** supplier and product registration, editing, active/inactive status, product stock history.
- **Inventory and sales:** stock adjustments, sales recording and details, sale voiding, stock movement history.
```

**File: NEW** `README.md`. **Code (exact PowerShell excerpt):**

```powershell
.\.venv\Scripts\python.exe manage.py check
.\.venv\Scripts\python.exe manage.py migrate
.\.venv\Scripts\python.exe manage.py test
.\.venv\Scripts\python.exe manage.py runserver
```

**Walkthrough.** A root README is GitHub's main entry point. Setup uses pinned dependency files and `.env.example`; the secret key must be replaced locally. The README states that a new database does not automatically contain users or demonstration records. It also explains that AI is not guaranteed to beat a simple baseline.

**How we checked it.** Reopened the published `README.md` and compared documented routes, roles, settings and dependencies with the source. No local installation or test execution was performed during this documentation step.

**Source commit:** `7b18ee9`.

**Next:** Step 535.

## Step 535 — Write and verify the five-minute demonstration script

**Before this step.** The system had many modules, and an unstructured demonstration could spend too much time on forms while failing to explain its AI features and business purpose.

**What we did.** Created a timed presentation: Manager dashboard, catalog, sales, purchase orders, demand intelligence, historical comparison and summary. Added common academic-review questions and a pre-presentation checklist.

**File: NEW** `DEMO_SCRIPT.md`. **Code (exact Markdown excerpt):**

```markdown
## Key explanations for questions

**Why Django?** It provides authentication, forms, ORM-backed database access, routing and templates for the complete application.

**Where is AI used?** Chronos-Bolt Tiny produces historical-data-based demand forecasts. Shortage and reorder services consume forecast information; unusual-sales detection uses a statistical comparison, not a separate trained model.

**How do you prevent look-ahead bias?** Historical forecasts are requested using the day before each evaluated week. Actual sales for that week are held out for comparison.
```

**Walkthrough.** The script distinguishes an actual live calculation from previously cached results. It instructs the presenter to prepare demo accounts and data, run tests, and use current screen values if they differ from the recorded example. It does not claim the UI refinement phase is complete.

**How we checked it.** Reopened both documentation files from GitHub and checked the referenced Django routes, role restriction, package pins and example metrics against the inspected source and reported live evaluation. The published documents exist on `main`. The previous 528-test result belongs to Chapter 2; it was **not rerun** for these documentation-only changes.

**Source commit:** `951e5be`.

**Next:** Module 9 development, followed by the separately promised UI refinement phase and Module 10 finalization.

## Differences from class — NEW vs CLASS

- **NEW — Root project README:** `README.md` describes the integrated system rather than only the Module 0 exercise. The original `README_M0.md` remains unchanged.
- **NEW — Demonstration script:** `DEMO_SCRIPT.md` provides a timed academic explanation. It is documentation, not a new Django view or feature.
- **CLASS/EXISTING — Django configuration:** `config/urls.py`, account roles, and installed apps were inspected but not modified.

## Chapter status

**Documentation created and source-verified.** Both files were committed to source `main`. No local execution or new regression test run was claimed for this documentation-only chapter. Module 8 Chapter 3 is closed; pending development and UI refinements remain separate.
