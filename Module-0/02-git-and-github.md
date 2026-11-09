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
