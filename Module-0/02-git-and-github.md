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
- [Step 17: Understand staging versus committing](#step-17)
- [Step 18: Stage the private journal](#step-18)
- [Step 19: Commit the private development journal](#step-19)
- [Step 20: Push your journal to private GitHub](#step-20)
- [Step 21: Understand local and remote main branches](#step-21)
- [Step 22: Apply database migrations](#step-22)

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


---

<a id="step-17"></a>

### Step 17: Understand staging versus committing

#### Before this step

While learning Git, we needed an example that made “edit a file” different from “save an edit in Git.”. Immediately before this, Step 16 dealt with stage the journal for git; the work here continued the same part of the application.

#### What we did and why

Changing views.py changes the working file, but Git does not create a snapshot automatically. You must stage and commit. The code discussed here was used to explain that difference; it was not the start of a new inventory feature.

#### Code and commands

**Step 17, block 1.** This code sample or terminal command was used while working on understand staging versus committing. The content below remains in its original sequence for Step 17, block 1.

```bash
git add views.py
```

**Step 17, block 2.** This code sample or terminal command was used while working on understand staging versus committing. The content below remains in its original sequence for Step 17, block 2.

```bash
git commit -m "Add inventory views"
```

#### How the code works

**Block 1 (bash, 1 lines).** In Step 17, understand staging versus committing involved `views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git add views.py` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (bash, 1 lines).** In Step 17, understand staging versus committing involved `views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git commit -m "Add inventory views"` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 17. For Step 17, the proposed way to check understand staging versus committing was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 18 before a complete result could be claimed.

#### Next

[Step 16: Stage the journal for Git](#step-16) · [Step 18: Stage the private journal](#step-18)


---

<a id="step-18"></a>

### Step 18: Stage the private journal

#### Before this step

After that explanation, we returned to our actual journal folder and selected the journal file for saving. Immediately before this, Step 17 dealt with understand staging versus committing; the work here continued the same part of the application.

#### What we did and why

This repeated staging step mattered because we were working with two separate Git repositories. It made sure the journal, not the application code, was the file being prepared.

#### Code and commands

**Step 18, block 1.** This code sample or terminal command was used while working on stage the private journal. The content below remains in its original sequence for Step 18, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**Step 18, block 2.** This code sample or terminal command was used while working on stage the private journal. The content below remains in its original sequence for Step 18, block 2.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal diff --cached --name-only
```

**Step 18, block 3.** This output was used while working on stage the private journal. The content below remains in its original sequence for Step 18, block 3.

```text
inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 18, stage the private journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 18, stage the private journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal diff --ca` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (text, 1 lines).** In Step 18, stage the private journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 18, output block 3 showed the result rather than another command. Its visible value was `inventory-development-journal.md`, which mattered for stage the private journal.

#### How we checked it

Not tested yet at Step 18. For Step 18, the proposed way to check stage the private journal was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 19 before a complete result could be claimed.

#### Next

[Step 17: Understand staging versus committing](#step-17) · [Step 19: Commit the private development journal](#step-19)


---

<a id="step-19"></a>

### Step 19: Commit the private development journal

#### Before this step

The journal file was selected. We could now save its first version in the journal's own history. Immediately before this, Step 18 dealt with stage the private journal; the work here continued the same part of the application.

#### What we did and why

The commit records what the journal said at that moment. If the written explanation change later, we will still be able to see this earlier version.

#### Code and commands

**Step 19, block 1.** This code sample or terminal command was used while working on commit the private development journal. The content below remains in its original sequence for Step 19, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: add initial development and setup journal"
```

**Step 19, block 2.** This code sample or terminal command was used while working on commit the private development journal. The content below remains in its original sequence for Step 19, block 2.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote -v
```

**Step 19, block 3.** This output was used while working on commit the private development journal. The content below remains in its original sequence for Step 19, block 3.

```text
origin  https://github.com/mehreen-7547/inventory-development-journal.git (fetch)
origin  https://github.com/mehreen-7547/inventory-development-journal.git (push)
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 19, commit the private development journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 19, commit the private development journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal remote -v` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (text, 2 lines).** In Step 19, commit the private development journal involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 19, output block 3 showed the result rather than another command. Its visible value was `origin  https://github.com/mehreen-7547/inventory-development-journal.git (fetch)`, which mattered for commit the private development journal.

#### How we checked it

Not tested yet at Step 19. For Step 19, the proposed way to check commit the private development journal was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 20 before a complete result could be claimed.

#### Next

[Step 18: Stage the private journal](#step-18) · [Step 20: Push your journal to private GitHub](#step-20)


---

<a id="step-20"></a>

### Step 20: Push your journal to private GitHub

#### Before this step

The journal commit was safe locally but still absent from its GitHub repository. Immediately before this, Step 19 dealt with commit the private development journal; the work here continued the same part of the application.

#### What we did and why

Pushing copies the saved journal version to its online destination. It does not publish the Django application or merge the two repositories.

#### Code and commands

**Step 20, block 1.** This code sample or terminal command was used while working on push your journal to private github. The content below remains in its original sequence for Step 20, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push -u origin main
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 20, push your journal to private github involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push -u o` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 20. For Step 20, the proposed way to check push your journal to private github was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 21 before a complete result could be claimed.

#### Next

[Step 19: Commit the private development journal](#step-19) · [Step 21: Understand local and remote main branches](#step-21)


---

<a id="step-21"></a>

### Step 21: Understand local and remote main branches

#### Before this step

Both the application and journal used a branch named main, which could make it seem as though they were connected. Immediately before this, Step 20 dealt with push your journal to private github; the work here continued the same part of the application.

#### What we did and why

A branch is one line of history inside one repository. Two different repositories can each have a main branch without sharing files. A local main is your working version; origin/main describes the version known on GitHub.

#### Code and commands

**Step 21, block 1.** This code sample or terminal command was used while working on understand local and remote main branches. The content below remains in its original sequence for Step 21, block 1.

```powershell
git push -u origin main
```

**Step 21, block 2.** This code sample or terminal command was used while working on understand local and remote main branches. The content below remains in its original sequence for Step 21, block 2.

```powershell
git push
```

**Step 21, block 3.** This code sample or terminal command was used while working on understand local and remote main branches. The content below remains in its original sequence for Step 21, block 3.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py showmigrations
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 21, understand local and remote main branches involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git push -u origin main` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 2 (powershell, 1 lines).** In Step 21, understand local and remote main branches involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git push` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (powershell, 1 lines).** In Step 21, understand local and remote main branches involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py showmigrations` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 21. For Step 21, the proposed way to check understand local and remote main branches was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 22 before a complete result could be claimed.

#### Next

[Step 20: Push your journal to private GitHub](#step-20) · [Step 22: Apply database migrations](#step-22)


---

<a id="step-22"></a>

### Step 22: Apply database migrations

#### Before this step

Django had been installed, but the database still lacked the tables Django needs for users, sessions, and the administration site. Immediately before this, Step 21 dealt with understand local and remote main branches; the work here continued the same part of the application.

#### What we did and why

A database table is a place where Django stores a type of information. The migration command creates or changes tables according to Django's prepared instructions. We needed these basic tables before user accounts could work.

#### Code and commands

**Step 22, block 1.** This code sample or terminal command was used while working on apply database migrations. The content below remains in its original sequence for Step 22, block 1.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py migrate
```

**Step 22, block 2.** This code sample or terminal command was used while working on apply database migrations. The content below remains in its original sequence for Step 22, block 2.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py showmigrations
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 22, apply database migrations involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py migrate` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 22, apply database migrations involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py showmigrations` starts Python or one of its tools from the development terminal.

#### How we checked it

For Step 22 (Apply database migrations), we compared the visible terminal output with the requested action. The visible result for Step 22 was described as “your output shows”; this only addressed the apply database migrations check in `manage.py`.

#### Next

[Step 21: Understand local and remote main branches](#step-21) · [Step 23: Update your private development journal](#step-23)
