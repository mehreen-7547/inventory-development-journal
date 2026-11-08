# Module 0, Chapter 7: Module 0 completion

## Step 46: Update Module 0 Report

#### Step 60 — Replace the report content

In the opened `MODULE0_REPORT.md` file:

1. Press `Ctrl + A` to select everything.
2. Replace it with the following report.
3. Press `Ctrl + S` to save.

This preserves the required **a–h structure** and adds our verified results.

#### Module 0 Report — Django, SQLite and AI Feasibility

**Status:** Local verification completed. Final security audit, documentation update and completion tag pending.

#### a) Built (five lines)

1. Configured Django 6.0.9 with SQLite using environment variables.
2. Retained optional MySQL configuration for a future approved migration.
3. Verified conditional stock deduction and transaction rollback using temporary test models.
4. Tested Chronos-Bolt Tiny and Chronos-2 locally using pretrained weights on CPU.
5. Selected **Chronos-Bolt Tiny as the default forecasting model** based on lower memory usage.

#### b) Files created/changed

- `config/settings.py` — environment-based database configuration and application settings.
- `.env.example` — safe configuration template.
- `core/tests.py` — five automated tests.
- `scripts/ai_probe.py` — CPU forecasting feasibility test.
- `requirements.txt` — pinned core dependencies.
- `requirements-ai.txt` — pinned AI dependencies.
- `README_M0.md` — installation and execution instructions.
- `SQLITE_ARCHITECTURE_AMENDMENT.md` — database and stock-update decisions.
- `MODULE0_REPORT.md` — verification results and conclusions.

No production inventory or sales models have been implemented.

#### c) Commands executed

```powershell
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
python .\techmindz_inventory_module0_sqlite_atomic\manage.py migrate
python .\techmindz_inventory_module0_sqlite_atomic\manage.py test core
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver

python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt

python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py --model bolt
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py
```

Commands were executed from the parent project directory with the virtual environment activated.

#### d) Manual checks and actual results

**Development environment**

- Python 3.12.12
- Django 6.0.9
- SQLite database initialized successfully.
- All 18 initial Django migrations applied.
- Development server and homepage worked.
- Django admin login verified.
- `.env` and `db.sqlite3` confirmed ignored and untracked.

**AI feasibility — both-model run**

| Measurement | Chronos-Bolt Tiny | Chronos-2 |
|---|---|---|
| Status | PASS | PASS |
| Total execution | 7.30 seconds | 59.74 seconds |
| Peak process memory | 363.5 MB | 811.8 MB |
| Forecast computation | 0.01 seconds | 0.05 seconds |
| First-day P10 | 7.37 | 8.85 |
| First-day P50 | 8.42 | 9.03 |
| First-day P90 | 9.39 | 9.15 |

Both models received the same 90-day synthetic sales history and generated 30-day forecasts.

Chronos-2's execution time included its initial model download. Total execution times are therefore not directly comparable.

**Model recommendation:** Chronos-Bolt Tiny is the default because it completed the test successfully while using substantially less memory. Chronos-2 remains an alternative for later evaluation.

#### e) Automated tests and failure conditions

**Result: 5 tests passed, 0 failures.**

1. Homepage test — verifies successful response and expected content.
2. Timezone test — verifies `Asia/Kolkata` and `USE_TZ = True`.
3. SQLite test — verifies the configured database connection.
4. Last-unit stock test — verifies that a second sequential deduction is refused.
5. Transaction rollback test — verifies that stock is restored after a simulated failure.

The stock tests use temporary models and tables, not production inventory models.

#### f) Assumptions, decisions and limitations

**Confirmed decisions**

- Python 3.12 and Django 6.0.9.
- SQLite for local development instead of MySQL.
- Database settings read from `.env`, allowing a later approved MySQL migration.
- Chronos-Bolt Tiny as the default forecasting model.
- Separate core and AI dependency files.
- CPU-only forecasting without model training or paid APIs.

**Limitations**

- SQLite sequential tests do not prove safety or throughput under simultaneous requests. Concurrent writes may encounter database-lock errors.
- Actual Product, Sale and StockMovement models and services remain unimplemented.
- Forecast accuracy has not been established.
- Formal backtesting, baseline comparison and P10–P90 interval coverage are deferred to Module 8.
- AI dependencies are used by the standalone forecasting script, not required for the current Django application's startup.
- Real inventory tests must be introduced when production models and stock services are implemented.
- Final Git history security verification remains pending.

#### g) Suggested Git commit message

`docs: finalize Module 0 verification and model recommendation`

After completing all mandatory checks, tag the verified commit:

`m0-complete`

#### h) Interview questions

**1. How do you prevent stock from becoming negative?**

Use an atomic conditional database update inside a transaction. The deduction succeeds only when sufficient stock exists.

**2. What happens if a sale fails after stock is deducted?**

The database transaction rolls back, restoring the original stock quantity.

**3. Why did you select Chronos-Bolt Tiny?**

Both pretrained models worked locally, but Chronos-Bolt Tiny used substantially less memory. Forecast accuracy will be evaluated later before any final production decision.

#### Learn this

- **Virtual environment:** Isolates project-specific Python packages.
- **`.env`:** Stores local configuration separately from source code.
- **SQLite:** Provides a file-based relational database.
- **`transaction.atomic()`:** Ensures grouped database operations commit or roll back together.
- **Conditional update:** Changes stock only when a database-level condition is satisfied.
- **Forecast quantiles:** P10, P50 and P90 represent different points in a predictive distribution, not guaranteed minimum and maximum sales.

**Three Django concepts:** Settings configuration, automated test cases, and ORM conditional updates.

Once saved, reply **done**. We'll update the README next, then complete the remaining checks before committing and tagging.

#### How we checked it

#### Step 61 — Open the README

The report has been saved locally. Next, we'll update the README to reflect the completed tests and model recommendation.

Run:

```powershell
code .\techmindz_inventory_module0_sqlite_atomic\README_M0.md
```

We'll update the outdated setup status, record **Chronos-Bolt Tiny as the default model**, and ensure the instructions match your actual project directory.

Reply **opened** when the file is open.

#### How we checked it

#### Step 63 — Check which files have changed

Before committing, we'll verify that Git recognizes our documentation updates.

Run this command in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status --short
```

**Expected output:**

```text
 M MODULE0_REPORT.md
 M README_M0.md
```

- `M` means the file has been modified.
- Both files should appear because we edited them.
- No `.env`, database, or other sensitive files should appear.

This command only checks Git's status. It does not change anything.

After confirming the output, click **Next**.

 GenUI.issueNewTurn("next")}>Next

#### How we checked it

#### Step 64 — Check for formatting errors

Before committing our updated report and README, run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic diff --check
```

**What this does:** Checks your modified files for common formatting problems, such as trailing whitespace.

**Expected result:** No output. This means Git found no whitespace errors.

This does not modify your files or commit anything.

If an error appears, share it. Otherwise, continue.

#### How we checked it

#### Step 65 — Check Git history for sensitive files

Before finalizing Module 0, we need to ensure that private files were never committed to the public repository.

Run this in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic log --all --format= --name-only | Sort-Object -Unique
```

This lists every filename recorded in Git history.

**Check that these are absent:**
- `.env` (but `.env.example` is safe)
- `db.sqlite3`
- Private keys or credential files

This checks filenames only. We'll separately check the committed contents for exposed secrets.

If you see anything suspicious, share the filename. Otherwise, click **Next**.

#### How we checked it

**Step 65 passed.** Your Git history contains only the expected 20 project files. Neither `.env` nor `db.sqlite3` appears.

#### Step 66 — Check committed files for potential secrets

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic grep -I -l -i -E 'secret_key|password|api_key|access_token|private.key' $(git -C .\techmindz_inventory_module0_sqlite_atomic rev-list --all)
```

This searches committed file contents across Git history for terms commonly associated with credentials.

**Important:** It displays filenames, not secret values. Files such as `.env.example` and `config/settings.py` may appear legitimately.

Share the output so we can review any matches.

#### How we checked it

#### Step 68 — Verify documentation changes

Our security checks are complete. Before committing, let's confirm which files have changed.

Run in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic diff --stat
```

**Expected:** Only these two files should appear:

- `MODULE0_REPORT.md`
- `README_M0.md`

The command shows how many lines changed in each file. It does not modify anything.

If other files appear, share the output. Otherwise, proceed.

*Previous: Step 45 (Documenting Model Results)  |  Next: Step 47 (Stage Documentation)*

---

## Step 47: Stage Documentation

#### Step 69 — Stage the updated documentation

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add MODULE0_REPORT.md README_M0.md
```

This prepares **only these two files** for the next commit. Nothing is uploaded to GitHub yet.

*Previous: Step 46 (Update Module 0 Report)  |  Next: Step 48 (Commit Documentation)*

---

## Step 48: Commit Documentation

#### Step 70 — Commit the Module 0 documentation

Run in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "docs: record Module 0 verification and AI feasibility results"
```

This saves the staged changes as a new commit in your **local Git history**.

It does not upload anything to GitHub yet.

*Previous: Step 47 (Stage Documentation)  |  Next: Step 49 (Push Documentation)*

---

## Step 49: Push Documentation

#### Step 71 — Push the commit to GitHub

Run in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push origin main
```

This uploads your committed Module 0 documentation changes to the **public GitHub repository**.

Expected output includes:

```text
main -> main
```

If Git reports an error, share the output before continuing.

*Previous: Step 48 (Commit Documentation)  |  Next: Step 50 (Finalize Report)*

---

## Step 50: Finalize Report

**Step 71 verified.** Your new commit `93978f8` is visible on GitHub.

#### Step 72 — Update the report's completion status

Open `MODULE0_REPORT.md`.

Replace its existing `**Status:**` line with:

```markdown
**Status:** Module 0 implementation, local verification, documentation, and basic Git security checks completed. Completion tag pending. Architecture and permissions approval required before Module 1.
```

Save using `Ctrl + S`.

*Previous: Step 49 (Push Documentation)  |  Next: Step 51 (Finalize README)*

---

## Step 51: Finalize README

#### Step 73 — Update the README completion status

Open `README_M0.md` and make two changes.

**1. Replace the Status line with:**

```markdown
**Status:** Module 0 implementation, local verification, documentation, and basic Git security checks completed. Completion tag pending. Architecture and permissions approval required before Module 1.
```

**2. Replace item 5 under Module 0 acceptance checks with:**

```markdown
5. PASS — Module 0 verification, documentation, and basic security checks completed. PENDING — Create the `m0-complete` tag and obtain architecture and permissions approval before Module 1.
```

Save with `Ctrl + S`.

*Previous: Step 50 (Finalize Report)  |  Next: Step 52 (Stage Final Updates)*

---

## Step 52: Stage Final Updates

#### Step 74 — Stage the final documentation updates

Run in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add MODULE0_REPORT.md README_M0.md
```

This stages the two updated files for the final Module 0 documentation commit. It does not upload anything.

*Previous: Step 51 (Finalize README)  |  Next: Step 53 (Final Documentation Commit)*

---

## Step 53: Final Documentation Commit

#### Step 75 — Save and push the final changes

Run these three commands together in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add MODULE0_REPORT.md README_M0.md
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "docs: finalize Module 0 completion status"
git -C .\techmindz_inventory_module0_sqlite_atomic push origin main
```

This stages, commits, and uploads the final documentation changes.

**Next:** Create the `m0-complete` Git tag. If any command fails, share the error instead of proceeding.

*Previous: Step 52 (Stage Final Updates)  |  Next: Step 54 (Create Completion Tag)*

---

## Step 54: Create Completion Tag

**Step 75 verified.** Your latest commit `9d1a112` is on GitHub.

#### Step 76 — Create the Module 0 completion tag

Run this command:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic tag -a m0-complete -m "Module 0 completed and verified"
```

**What is a Git tag?**

A tag permanently identifies a specific commit. Here, `m0-complete` marks the version of your project that completed Module 0.

Unlike a branch, the tag does not automatically move when you make new commits.

This command creates the tag locally. We'll upload it to GitHub next.

*Previous: Step 53 (Final Documentation Commit)  |  Next: Step 55 (Publish Completion Tag)*

---

## Step 55: Publish Completion Tag

#### Step 77 — Push the completion tag to GitHub

Run in your VS Code terminal:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push origin m0-complete
```

**Expected output:**

```text
[new tag] m0-complete -> m0-complete
```

This publishes the `m0-complete` tag to GitHub, marking the completed Module 0 version.

After this, we'll prepare the **application architecture and role-permission plan** for approval before starting Module 1.

*Previous: Step 54 (Create Completion Tag)  |  Next: Step 56 (Approved — Begin Development)*

---
