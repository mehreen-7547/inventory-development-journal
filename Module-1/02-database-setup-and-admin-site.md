# MODULE 1 — Employee accounts

## Chapter 2: Database setup and admin site (Steps 68–72)

### Before we begin

We had written our new employee user model, but the old test database was prepared for Django's default user model.

### What we worked on

While the database still contained only disposable development data, we reset it, created tables for the new model, made an administrator and registered users in Django Admin.

### Picture it this way

This is an early-development correction. On a live system, deleting the database would lose real records, so a database reset is not a normal way to update employee models.

### Find a step

- [Step 68: Database Reset](#step-68)

---

<a id="step-68"></a>

### Step 68: Database Reset

#### Before this step

The starter database had already been created before our custom user model existed. That could leave early development tables referring to the wrong user type. The preceding step, Step 67, covered migration preparation, so this was the next part of the development sequence.

#### What we did and why

Because this was still a disposable test database, the written explanation show resetting it and rebuilding with the chosen user model. This is not something to repeat with real employee records: deleting a database also deletes its data.

#### Code and commands

**Step 68, block 1.** This code sample or terminal command was used while working on database reset. The content below remains in its original sequence for Step 68, block 1.

```powershell
Remove-Item .\db.sqlite3
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 68, database reset involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Remove-Item .\db.sqlite3` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 68. For Step 68, the proposed way to check database reset was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 69 before a complete result could be claimed.

#### Next

[Step 67: Migration Preparation](../Module-1/01-accounts-app-and-custom-user-model.md#step-67) · [Step 69: Database File Locked](#step-69)
