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
