# Module 0, Chapter 3: Database and migrations

## Step 25: Committing Development Journal Update

**Step 33 completed.** The journal was staged successfully. The LF/CRLF warning is harmless.

#### Step 34 — Commit the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**What this does:** Creates a new local Git commit containing your migration notes.

It does not upload anything to GitHub yet.

Send me the output, and we'll push the commit to your private repository.

*Previous: Step 24 (Stage the updated journal)  |  Next: Step 26 (Committing Development Journal Update)*

---

## Step 26: Committing Development Journal Update

#### Step 34 — Commit the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record Django database initialization"
```

**What this does:** Saves the staged migration notes as a new commit in your local private journal repository.

It does not upload anything to GitHub yet.

Send me the terminal output. Next, we'll push the commit to GitHub.

*Previous: Step 25 (Committing Development Journal Update)  |  Next: Step 27 (Pushing Development Journal to GitHub)*

---

## Step 27: Pushing Development Journal to GitHub

#### Step 35 — Push the journal update

Run:

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal push
```

**What this does:** Uploads your latest local journal commit to your private GitHub repository.

Because we previously configured the tracking branch, `git push` is sufficient.

**Expected result:** A message showing `main -> main`.

Send me the output. We'll verify the upload before continuing Django development.

#### How we checked it

**Step 35 completed.** I verified that your latest journal commit is available in your private GitHub repository.

#### Step 36 — Start the Django development server

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py runserver
```

**What this does:**
- Starts Django's local development server.
- Checks whether the project can start successfully.
- Makes the application accessible through your browser.

**Expected output:**

```text
Starting development server at http://127.0.0.1:8000/
```

The terminal will remain occupied while the server is running. This is normal.

Send me the terminal output. We'll then test the application in your browser.

*Previous: Step 26 (Committing Development Journal Update)  |  Next: Step 28 (Understanding the Migration Workflow)*

---
