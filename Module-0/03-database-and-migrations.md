# MODULE 0 — Foundations

## Chapter 3: Database and migrations (Steps 25–28)

**Where we are.** Django's model definitions become real database tables only after migrations. We recorded the initial schema work.

**What this chapter gives us.** Recognize migration files, database state, and the distinction between making and applying migrations.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain what a migration does and why a successful Git commit does not apply it.

### Find a step

- [Step 25: Committing Development Journal Update](#step-25)
- [Step 26: Clarify the journal commit](#step-26)
- [Step 27: Pushing Development Journal to GitHub](#step-27)
- [Step 28: Understanding the Migration Workflow](#step-28)

---

<a id="step-25"></a>

### Step 25: Committing Development Journal Update

**Why we are doing this.** After staging the migration notes, we needed to save them as a local journal revision.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**How it works.** The commit command captures the documentation snapshot. It does not redo the migrations.

**How we checked it.** This was followed by a repeated commit instruction rather than a new functional change.

**Connects to.** Previous: [Step 24 — Stage the updated journal](02-git-and-github.md#step-24) · Next: [Step 26 — Clarify the journal commit](03-database-and-migrations.md#step-26)


---

<a id="step-26"></a>

### Step 26: Clarify the journal commit

**Why we are doing this.** The previous commit instruction was repeated to clarify that committing and uploading are different operations.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**How it works.** The same `git commit` command was provided again. It should be read as the continuation of Step 25, not as an instruction to create duplicate commits.

**How we checked it.** No separate success result is recorded for this repetition.

**Connects to.** Previous: [Step 25 — Committing Development Journal Update](03-database-and-migrations.md#step-25) · Next: [Step 27 — Pushing Development Journal to GitHub](03-database-and-migrations.md#step-27)


---

<a id="step-27"></a>

### Step 27: Pushing Development Journal to GitHub

**Why we are doing this.** The local journal revision now needed to appear on GitHub.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run (historical; **do not rerun blindly**).*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How it works.** `git push` uploads commits on the configured tracking branch. It does not create new documentation content.

**How we checked it.** The following notes proceed with database and server study, consistent with the journal update being finished.

**Connects to.** Previous: [Step 26 — Clarify the journal commit](03-database-and-migrations.md#step-26) · Next: [Step 28 — Understanding the Migration Workflow](03-database-and-migrations.md#step-28)


---

<a id="step-28"></a>

### Step 28: Understanding the Migration Workflow

**Why we are doing this.** Now that migrations were applied, we checked what the operation actually accomplished rather than merely memorizing a command.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**.*

```powershell
python manage.py showmigrations
```

**How it works.** Django migrations create and evolve tables; `showmigrations` reports which migration files have been applied. A populated migration list does not mean business features have been implemented.

**How we checked it.** The earlier `[X]` results support that the initial Django database setup succeeded.

**Connects to.** Previous: [Step 27 — Pushing Development Journal to GitHub](03-database-and-migrations.md#step-27) · Next: [Step 29 — Confirming Terminal Environment](04-server-and-admin-account.md#step-29)

---

**Chapter recap.** Recognize migration files, database state, and the distinction between making and applying migrations.

**Practice explaining:** Explain what a migration does and why a successful Git commit does not apply it.

