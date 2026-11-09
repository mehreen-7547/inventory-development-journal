# MODULE 0 — Foundations

## Chapter 2: Git and GitHub (Steps 7–24)

### Before we begin

The starter code was on the laptop, which meant we could lose track of changes or accidentally mix project files with study notes.

### What we worked on

We put the application code under Git, connected it to GitHub, and made a separate private repository for the learning journal.

### Picture it this way

A Git commit is a saved version on the laptop. A Git push sends that saved version to GitHub. We practise these actions repeatedly because they happen after nearly every development milestone.

### Find a step

- [Step 7: Initialize the Git repository](#step-7)
- [Step 8: Stage the project files](#step-8)
- [Step 9: Create our first Git commit](#step-9)

---

<a id="step-7"></a>

### Step 7: Initialize the Git repository

#### Before this step

The starter project was on the laptop. We wanted a record of every change so an accidental edit would not destroy our work. The preceding step, Step 6, covered create .env, so this was the next part of the development sequence.

#### What we did and why

Git is a tool that keeps a history of changes to files. git init starts that history inside the project folder; it does not upload anything to the internet.

#### Code and commands

**Step 7, block 1.** This code sample or terminal command was used while working on initialize the git repository. The content below remains in its original sequence for Step 7, block 1.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic init -b main
```

**Step 7, block 2.** This code sample or terminal command was used while working on initialize the git repository. The content below remains in its original sequence for Step 7, block 2.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status --short --untracked-files=all
```

**Step 7, block 3.** This code sample or terminal command was used while working on initialize the git repository. The content below remains in its original sequence for Step 7, block 3.

```powershell
Get-Content .\techmindz_inventory_module0_sqlite_atomic\.env.example
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 7, initialize the git repository involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic init -b main` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 7, initialize the git repository involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic status --short --untracked-files=all` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (powershell, 1 lines).** In Step 7, initialize the git repository involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Get-Content .\techmindz_inventory_module0_sqlite_atomic\.env.example` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

#### How we checked it

For Step 7 (Initialize the Git repository), we compared the visible terminal output with the requested action. The visible result for Step 7 was described as “was successful”; this only addressed the initialize the git repository check in `VS Code terminal / project setup`.

#### Next

[Step 6: Create .env](../Module-0/01-environment-setup.md#step-6) · [Step 8: Stage the project files](#step-8)


---

<a id="step-8"></a>

### Step 8: Stage the project files

#### Before this step

Git had been switched on, but it had not been told which changed files to save in the next snapshot. Immediately before this, Step 7 dealt with initialize the git repository; the work here continued the same part of the application.

#### What we did and why

git add puts chosen files into a temporary list for the next save. This list is called the staging area. Nothing has been saved as a commit or sent to GitHub yet.

#### Code and commands

**Step 8, block 1.** This code sample or terminal command was used while working on stage the project files. The content below remains in its original sequence for Step 8, block 1.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic add .
```

**Step 8, block 2.** This code sample or terminal command was used while working on stage the project files. The content below remains in its original sequence for Step 8, block 2.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic diff --cached --name-only
```

**Step 8, block 3.** This code sample or terminal command was used while working on stage the project files. The content below remains in its original sequence for Step 8, block 3.

```powershell
.\.venv\Scripts\python.exe .\techmindz_inventory_module0_sqlite_atomic\manage.py check
```

**Step 8, block 4.** This output was used while working on stage the project files. The content below remains in its original sequence for Step 8, block 4.

```text
System check identified no issues (0 silenced).
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 8, stage the project files involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic add .` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 8, stage the project files involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic diff --cached --name-only` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (powershell, 1 lines).** In Step 8, stage the project files involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe .\techmindz_inventory_module0_sqlite_atomic\manage.py chec` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

**Block 4 (text, 1 lines).** In Step 8, stage the project files involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 8, output block 4 showed the result rather than another command. Its visible value was `System check identified no issues (0 silenced).`, which mattered for stage the project files.

#### How we checked it

Not tested yet at Step 8. For Step 8, the proposed way to check stage the project files was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 9 before a complete result could be claimed.

#### Next

[Step 7: Initialize the Git repository](#step-7) · [Step 9: Create our first Git commit](#step-9)


---

<a id="step-9"></a>

### Step 9: Create our first Git commit

#### Before this step

The correct files were now selected. We needed to save the first meaningful version of the project. Immediately before this, Step 8 dealt with stage the project files; the work here continued the same part of the application.

#### What we did and why

A commit is a named snapshot of the selected files. After this, Git can show what changed between this version and later ones. The commit stays on the laptop until we upload it.

#### Code and commands

**Step 9, block 1.** This output was used while working on create our first git commit. The content below remains in its original sequence for Step 9, block 1.

```text
System check identified no issues (0 silenced).
```

**Step 9, block 2.** This code sample or terminal command was used while working on create our first git commit. The content below remains in its original sequence for Step 9, block 2.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "chore: initialize Django project (module 0)"
```

#### How the code works

**Block 1 (text, 1 lines).** In Step 9, create our first git commit involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 9, output block 1 showed the result rather than another command. Its visible value was `System check identified no issues (0 silenced).`, which mattered for create our first git commit.

**Block 2 (powershell, 1 lines).** In Step 9, create our first git commit involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic commit -m "chore: initialize Django pro` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 9. For Step 9, the proposed way to check create our first git commit was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 10 before a complete result could be claimed.

#### Next

[Step 8: Stage the project files](#step-8) · [Step 10: Connect your local project to GitHub](#step-10)
