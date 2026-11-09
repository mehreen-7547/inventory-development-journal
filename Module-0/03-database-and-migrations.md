# MODULE 0 — Foundations

## Chapter 3: Database and migrations (Steps 25–28)

### Before we begin

Django was installed, but its database tables still had to be created, and our notes had to record the work.

### What we worked on

We ran the database setup and then saved the result in the journal. Some steps repeat Git commands because the original development sequence contains each save and upload separately.

### Picture it this way

The key difference is between a database change and a journal update. Writing notes about a table is not the same as creating that table.

### Find a step

- [Step 25: Committing Development Journal Update](#step-25)

---

<a id="step-25"></a>

### Step 25: Committing Development Journal Update

#### Before this step

The database milestone was staged, and the journal needed a new saved version. The preceding step, Step 24, covered stage the updated journal, so this was the next part of the development sequence.

#### What we did and why

The commit adds a restore point to the journal's history. It records the documentation change, not a new database migration.

#### Code and commands

**Step 25, block 1.** This code sample or terminal command was used while working on committing development journal update. The content below remains in its original sequence for Step 25, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 25, committing development journal update involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 25. For Step 25, the proposed way to check committing development journal update was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 26 before a complete result could be claimed.

#### Next

[Step 24: Stage the updated journal](../Module-0/02-git-and-github.md#step-24) · [Step 26: Clarify the journal commit](#step-26)
