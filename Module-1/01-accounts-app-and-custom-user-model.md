# MODULE 1 — Employee accounts

## Chapter 1: Accounts app and custom user model (Steps 56–67)

### Before we begin

The basic Django project could run, but it still treated users as generic Django accounts. Our business needs employees with different jobs.

### What we worked on

We created an accounts app and described a user record with roles for administrator, inventory manager and sales staff. We also learnt why Django already provides many username and password fields.

### Picture it this way

Every person signs in through the same Django login system, but their role helps us decide which pages to allow later. A model describes the employee entry; it does not become a database table until migrations are run.

### Find a step

- [Step 56: Begin the Accounts module](#step-56)

---

<a id="step-56"></a>

### Step 56: Begin the Accounts module

#### Before this step

Up to this point, we had mostly prepared Django and the development environment. Now we started the first feature ordinary users would interact with: accounts. The preceding step, Step 55, covered publish completion tag, so this was the next part of the development sequence.

#### What we did and why

The inventory system needed different people to sign in. We planned an administrator, an inventory manager, and sales staff. These roles would later decide who could open which pages.

#### Code and commands

**Step 56, block 1.** This code sample or terminal command was used while working on begin the accounts module. The content below remains in its original sequence for Step 56, block 1.

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

#### How the code works

**Block 1 (powershell, 2 lines).** In Step 56, begin the accounts module involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `python manage.py startapp accounts` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 56. For Step 56, the proposed way to check begin the accounts module was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 57 before a complete result could be claimed.

#### Next

[Step 55: Publish Completion Tag](../Module-0/07-module-0-completion.md#step-55) · [Step 57: Custom User Model](#step-57)
