# MODULE 0 — Foundations

## Chapter 5: Security checks (Steps 37–41)

### Before we begin

Before continuing to features, we checked that local secrets would not be uploaded and that the early testing work was clearly documented.

### What we worked on

These steps mostly record, save and explain security checks. They are not five separate changes to the application's permission system.

### Picture it this way

Imagine keeping a notebook of checks performed before accepting more changes. We should be able to say what was checked, and also what was not proved by those checks.

### Find a step

- [Step 37: Staging Security Verification Notes](#step-37)
- [Step 38: Committing Security Verification Notes](#step-38)

---

<a id="step-37"></a>

### Step 37: Staging Security Verification Notes

#### Before this step

Before sharing project files, we checked that private settings and local database files were not being accidentally uploaded. The preceding step, Step 36, covered pushing development journal to github, so this was the next part of the development sequence.

#### What we did and why

A .gitignore file tells Git to ignore certain files, such as .env, when selecting new files. We noted these precautions so a future change would not accidentally publish a password or local data.

#### Code and commands

**Step 37, block 1.** This code sample or terminal command was used while working on staging security verification notes. The content below remains in its original sequence for Step 37, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 37, staging security verification notes involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 37. For Step 37, the proposed way to check staging security verification notes was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 38 before a complete result could be claimed.

#### Next

[Step 36: Pushing Development Journal to GitHub](../Module-0/04-server-and-admin-account.md#step-36) · [Step 38: Committing Security Verification Notes](#step-38)


---

<a id="step-38"></a>

### Step 38: Committing Security Verification Notes

#### Before this step

The security-check notes were complete, so we saved them as a journal commit. Immediately before this, Step 37 dealt with staging security verification notes; the work here continued the same part of the application.

#### What we did and why

This created a history entry about the checks. It did not, on its own, perform another security test.

#### Code and commands

**Step 38, block 1.** This code sample or terminal command was used while working on committing security verification notes. The content below remains in its original sequence for Step 38, block 1.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record Git security verification"
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 38, committing security verification notes involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 38. For Step 38, the proposed way to check committing security verification notes was to run `git status` and inspect the local or remote history for the expected change. Development continued in Step 39 before a complete result could be claimed.

#### Next

[Step 37: Staging Security Verification Notes](#step-37) · [Step 39: Pushing Security Verification Notes](#step-39)
