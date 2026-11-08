# MODULE 0 — Getting started

## Chapter 3: Database and migrations (Steps 25–28)

### Before we begin

Django was installed, but its database tables still had to be created, and our notes had to record the work.

### What we worked on

We ran the database setup and then saved the result in the journal. Some steps repeat Git commands because the original development record contains each save and upload separately.

### Picture it this way

The key difference is between a database change and a journal update. Writing notes about a table is not the same as creating that table.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 25: Committing Development Journal Update](#step-25)
- [Step 26: Clarify the journal commit](#step-26)
- [Step 27: Pushing Development Journal to GitHub](#step-27)
- [Step 28: Understanding the Migration Workflow](#step-28)

---

<a id="step-25"></a>

### Step 25: Committing Development Journal Update

**What was happening before this step?**

The database milestone was staged, and the journal needed a new saved version.

**What did we do, and what does it mean?**

The commit adds a restore point to the journal's history. It records the documentation change, not a new database migration.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**How did we know what happened?**

This was followed by a repeated commit instruction rather than a new functional change.

**Where to go next:** Previous: [Step 24 — Stage the updated journal](02-git-and-github.md#step-24) · Next: [Step 26 — Clarify the journal commit](03-database-and-migrations.md#step-26)


---

<a id="step-26"></a>

### Step 26: Clarify the journal commit

**What was happening before this step?**

We repeated the commit instruction because it was important to distinguish saving notes locally from uploading them.

**What did we do, and what does it mean?**

The key idea is that a commit happens on the laptop. It is normal for the remote GitHub journal to remain unchanged until a separate push is performed.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**How did we know what happened?**

No separate success result is recorded for this repetition.

**Where to go next:** Previous: [Step 25 — Committing Development Journal Update](03-database-and-migrations.md#step-25) · Next: [Step 27 — Pushing Development Journal to GitHub](03-database-and-migrations.md#step-27)


---

<a id="step-27"></a>

### Step 27: Pushing Development Journal to GitHub

**What was happening before this step?**

The latest journal commit still existed only locally.

**What did we do, and what does it mean?**

This push uploaded that recorded milestone to the private repository. The application and database were not moved by this command; only the Git history of the journal was sent.

**Code or command from this step**

*1. Run. **Historical only: do not run it without understanding its effect on current files.***

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How did we know what happened?**

The following notes proceed with database and server study, consistent with the journal update being finished.

**Where to go next:** Previous: [Step 26 — Clarify the journal commit](03-database-and-migrations.md#step-26) · Next: [Step 28 — Understanding the Migration Workflow](03-database-and-migrations.md#step-28)


---

<a id="step-28"></a>

### Step 28: Understanding the Migration Workflow

**What was happening before this step?**

We had run the migration command, but it was not enough to memorize the word `migrate`. We wanted to know what it actually did.

**What did we do, and what does it mean?**

Django looks at the migration instructions stored in project files, applies any outstanding changes to the database, and remembers which instructions it has already applied. Changing model code by itself is not enough to update an existing database.

**Code or command from this step**

*1. Example used to explain the idea (not necessarily a file edit).*

```powershell
python manage.py showmigrations
```

**Understanding the code, one piece at a time**

If we change a model and run only `makemigrations`, we have written instructions but have not changed the table. If we then run `migrate`, Django follows those instructions. This distinction comes up often in Django interviews.

**How did we know what happened?**

The earlier `[X]` results support that the initial Django database setup succeeded.

**Where to go next:** Previous: [Step 27 — Pushing Development Journal to GitHub](03-database-and-migrations.md#step-27) · Next: [Step 29 — Confirming Terminal Environment](04-server-and-admin-account.md#step-29)

---

### The point of this chapter

The key difference is between a database change and a journal update. Writing notes about a table is not the same as creating that table.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

