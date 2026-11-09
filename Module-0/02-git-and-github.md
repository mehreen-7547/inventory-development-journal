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
- [Step 10: Connect your local project to GitHub](#step-10)
- [Step 11: Connect your local project to GitHub](#step-11)
- [Step 12: Push your first commit to GitHub](#step-12)
- [Step 13: Create a separate local journal folder](#step-13)
- [Step 14: Initialize Git for the private journal](#step-14)
- [Step 15: Connect it to GitHub](#step-15)
- [Step 16: Stage the journal for Git](#step-16)

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


---

<a id="step-10"></a>

### Step 10: Connect your local project to GitHub

#### Before this step

We had a saved project on the laptop, but no connection to the GitHub repository that would hold its online copy. Immediately before this, Step 9 dealt with create our first git commit; the work here continued the same part of the application.

#### What we did and why

A remote is simply a GitHub destination. Giving it the nickname origin lets later commands send our commits there. Connecting a remote does not upload files by itself.

#### Code and commands

**Step 10, block 1.** This code sample or terminal command was used while working on connect your local project to github. The content below remains in its original sequence for Step 10, block 1.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 10, connect your local project to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/me` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 10. For Step 10, the proposed way to check connect your local project to github was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 11 before a complete result could be claimed.

#### Next

[Step 9: Create our first Git commit](#step-9) · [Step 11: Connect your local project to GitHub](#step-11)


---

<a id="step-11"></a>

### Step 11: Connect your local project to GitHub

#### Before this step

Because we also had a separate learning journal, we checked that the project folder was connected to the correct GitHub destination. Immediately before this, Step 10 dealt with connect your local project to github; the work here continued the same part of the application.

#### What we did and why

The same Git command can point two folders to different repositories. This check was about avoiding a mix-up: application code belongs in the application repository, and private notes belong in the journal repository.

#### Code and commands

**Step 11, block 1.** This code sample or terminal command was used while working on connect your local project to github. The content below remains in its original sequence for Step 11, block 1.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/mehreen-7547/ai-assisted-inventory-manager.git
```

**Step 11, block 2.** This code sample or terminal command was used while working on connect your local project to github. The content below remains in its original sequence for Step 11, block 2.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic remote -v
```

**Step 11, block 3.** This output was used while working on connect your local project to github. The content below remains in its original sequence for Step 11, block 3.

```text
origin  https://github.com/mehreen-7547/ai-assisted-inventory-manager.git (fetch)
origin  https://github.com/mehreen-7547/ai-assisted-inventory-manager.git (push)
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 11, connect your local project to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic remote add origin https://github.com/me` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 11, connect your local project to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic remote -v` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (text, 2 lines).** In Step 11, connect your local project to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 11, output block 3 showed the result rather than another command. Its visible value was `origin  https://github.com/mehreen-7547/ai-assisted-inventory-manager.git (fetch)`, which mattered for connect your local project to github.

#### How we checked it

Not tested yet at Step 11. For Step 11, the proposed way to check connect your local project to github was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 12 before a complete result could be claimed.

#### Next

[Step 10: Connect your local project to GitHub](#step-10) · [Step 12: Push your first commit to GitHub](#step-12)


---

<a id="step-12"></a>

### Step 12: Push your first commit to GitHub

#### Before this step

Our first project commit existed only on the laptop. We wanted a copy on GitHub as well. Immediately before this, Step 11 dealt with connect your local project to github; the work here continued the same part of the application.

#### What we did and why

git push sends local commits to the connected GitHub repository. This is different from committing: commit saves a version locally; push uploads it.

#### Code and commands

**Step 12, block 1.** This code sample or terminal command was used while working on push your first commit to github. The content below remains in its original sequence for Step 12, block 1.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic push -u origin main
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 12, push your first commit to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic push -u origin main` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 12. For Step 12, the proposed way to check push your first commit to github was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 13 before a complete result could be claimed.

#### Next

[Step 11: Connect your local project to GitHub](#step-11) · [Step 13: Create a separate local journal folder](#step-13)


---

<a id="step-13"></a>

### Step 13: Create a separate local journal folder

#### Before this step

We wanted to keep detailed study notes without mixing long explanations into the Django application's code folders. Immediately before this, Step 12 dealt with push your first commit to github; the work here continued the same part of the application.

#### What we did and why

We created a separate folder for the learning journal. Think of it as a notebook beside the project, not part of the running website. Its history and GitHub destination can be managed independently.

#### Code and commands

**Step 13, block 1.** This code sample or terminal command was used while working on create a separate local journal folder. The content below remains in its original sequence for Step 13, block 1.

```powershell
New-Item -ItemType Directory -Path C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal
```

**Step 13, block 2.** This output was used while working on create a separate local journal folder. The content below remains in its original sequence for Step 13, block 2.

```text
projects2026/
├── Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│   └── techmindz_inventory_module0_sqlite_atomic/
│       └── Django project (public Git repository)
│
└── inventory-development-journal/
    └── Learning journal (private Git repository)
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 13, create a separate local journal folder involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `New-Item -ItemType Directory -Path C:\Users\YOUR_WINDOWS_USERNAME\projects2026\invent` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

**Block 2 (text, 7 lines).** In Step 13, create a separate local journal folder involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 13, output block 2 showed the result rather than another command. Its visible value was `projects2026/`, which mattered for create a separate local journal folder.

#### How we checked it

Not tested yet at Step 13. For Step 13, the proposed way to check create a separate local journal folder was to revisit `VS Code terminal / project setup` and run the corresponding command or feature to see its result. Development continued in Step 14 before a complete result could be claimed.

#### Next

[Step 12: Push your first commit to GitHub](#step-12) · [Step 14: Initialize Git for the private journal](#step-14)


---

<a id="step-14"></a>

### Step 14: Initialize Git for the private journal

#### Before this step

The journal folder existed, but it did not yet have its own change history. Immediately before this, Step 13 dealt with create a separate local journal folder; the work here continued the same part of the application.

#### What we did and why

Running Git initialization inside the journal starts a second, independent history. Saving the journal does not automatically save or change the Django application.

#### Code and commands

**Step 14, block 1.** This code sample or terminal command was used while working on initialize git for the private journal. The content below remains in its original sequence for Step 14, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal init -b main
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 14, initialize git for the private journal involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal init -b m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 14. For Step 14, the proposed way to check initialize git for the private journal was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 15 before a complete result could be claimed.

#### Next

[Step 13: Create a separate local journal folder](#step-13) · [Step 15: Connect it to GitHub](#step-15)


---

<a id="step-15"></a>

### Step 15: Connect it to GitHub

#### Before this step

The private journal was ready locally. We needed to connect it to the right private GitHub repository. Immediately before this, Step 14 dealt with initialize git for the private journal; the work here continued the same part of the application.

#### What we did and why

This command tells the journal folder where its online copy lives. A private repository is intended to restrict who can see the written explanation, unlike a publicly visible project repository.

#### Code and commands

**Step 15, block 1.** This code sample or terminal command was used while working on connect it to github. The content below remains in its original sequence for Step 15, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote add origin https://github.com/mehreen-7547/inventory-development-journal.git
```

**Step 15, block 2.** This code sample or terminal command was used while working on connect it to github. The content below remains in its original sequence for Step 15, block 2.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote -v
```

**Step 15, block 3.** This output was used while working on connect it to github. The content below remains in its original sequence for Step 15, block 3.

```text
origin  https://github.com/mehreen-7547/inventory-development-journal.git (fetch)
origin  https://github.com/mehreen-7547/inventory-development-journal.git (push)
```

**Step 15, block 4.** This code sample or terminal command was used while working on connect it to github. The content below remains in its original sequence for Step 15, block 4.

```powershell
Get-ChildItem C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal -Name
```

**Step 15, block 5.** This output was used while working on connect it to github. The content below remains in its original sequence for Step 15, block 5.

```text
inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 15, connect it to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote ad` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 15, connect it to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote -v` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (text, 2 lines).** In Step 15, connect it to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 15, output block 3 showed the result rather than another command. Its visible value was `origin  https://github.com/mehreen-7547/inventory-development-journal.git (fetch)`, which mattered for connect it to github.

**Block 4 (powershell, 1 lines).** In Step 15, connect it to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Get-ChildItem C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journ` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

**Block 5 (text, 1 lines).** In Step 15, connect it to github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 15, output block 5 showed the result rather than another command. Its visible value was `inventory-development-journal.md`, which mattered for connect it to github.

#### How we checked it

Not tested yet at Step 15. For Step 15, the proposed way to check connect it to github was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 16 before a complete result could be claimed.

#### Next

[Step 14: Initialize Git for the private journal](#step-14) · [Step 16: Stage the journal for Git](#step-16)


---

<a id="step-16"></a>

### Step 16: Stage the journal for Git

#### Before this step

The first journal file had been written, but Git was not yet preparing to save it. Immediately before this, Step 15 dealt with connect it to github; the work here continued the same part of the application.

#### What we did and why

git add selects that one journal file for the next commit. This is useful because the journal could eventually contain many files and we may not want to commit all of them at once.

#### Code and commands

**Step 16, block 1.** This code sample or terminal command was used while working on stage the journal for git. The content below remains in its original sequence for Step 16, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 16, stage the journal for git involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 16. For Step 16, the proposed way to check stage the journal for git was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 17 before a complete result could be claimed.

#### Next

[Step 15: Connect it to GitHub](#step-15) · [Step 17: Understand staging versus committing](#step-17)
