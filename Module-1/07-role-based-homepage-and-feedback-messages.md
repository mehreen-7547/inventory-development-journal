# Module 1, Chapter 7: Role-based homepage and feedback messages

## Step 91: Role-Based Homepage

#### Next: Display different content based on user role

Our system has three roles: Admin, Inventory Manager, and Sales Staff.

Open **`core/templates/core/home.html`**.

Below the welcome message, add:

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

#### How it works

Django checks the logged-in user's `role` field and displays the matching message.

For example:

  Example: Logged in as manager1
  Welcome, manager1
  Role: Inventory Manager

  **Inventory Manager Dashboard**

**Important:** This only changes what users *see*. It does not yet prevent them from accessing restricted functionality. We'll implement actual role permissions separately.

**Test:** Log in as `manager1` and `sales1` separately. Each should see their corresponding dashboard heading.

*Previous: Step 90 (Change Password Link)  |  Next: Step 92 (Admin Link Visibility)*

---

## Step 92: Admin Link Visibility

#### Next: Show the Django Admin link only to authorized staff

Currently, everyone sees the **Open Django admin** link, even though they cannot necessarily access it.

Open **`core/home.html`**.

Replace:

```html
<p><a href="/admin/">Open Django admin</a></p>
```

With:

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

#### How it works

`request.user.is_staff` checks whether the logged-in user has Django staff access.

- `admin` (superuser) → sees the link.
- `manager1` → doesn't see the link.
- `sales1` → doesn't see the link.

**Important:** `is_staff` is a Django permission field. It is different from our custom `role` field.

**Expected result:** The admin link appears only for staff users.

This only controls link visibility. Django itself enforces access restrictions on `/admin/`.

*Previous: Step 91 (Role-Based Homepage)  |  Next: Step 93 (Admin Access)*

---

## Step 93: Admin Access

**No. Even before we added the `is_staff` condition to `home.html`, manager1 could not access Django's Admin panel.**

Here's why.

We created `manager1` with:

- `role = 'manager'`
- `is_staff = False`

Django already uses `is_staff` internally to decide who can access `/admin/`.

#### Before and after our change

| | Before | After |
|---|---|---|
| Manager sees "Open Django admin" link on homepage | Yes | No |
| Manager can access Django Admin panel | No | No |
| Admin superuser can access Django Admin panel | Yes | Yes |

#### So what did our code actually change?

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**It only hides the link from users who cannot access Django Admin anyway.**

We did not introduce a new security restriction. Django already had that restriction built in.

Also, when `manager1` visits `/admin/`, Django may show the Admin login screen, but **seeing that login screen does not mean they have permission to enter the Admin panel**.

*Previous: Step 92 (Admin Link Visibility)  |  Next: Step 94 (Admin Access Verified)*

---

## Step 94: Admin Access Verified

That is **the expected result**.

When `manager1` clicks **Open Django admin**:

1. The browser opens `/admin/`.
2. Django checks whether `manager1` has Admin access.
3. Because `is_staff=False`, Django displays the Admin login page instead of the dashboard.

So our existing access restriction is working as expected.

**The `is_staff` condition we added to `home.html` is only meant to hide the Admin link from manager1.** It does not create the access restriction.

One thing remains: your screenshot still shows that link for manager1. Make sure you have saved this change in `home.html`:

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

After refreshing the homepage, the link should disappear.

*Previous: Step 93 (Admin Access)  |  Next: Step 95 (Login Error Messages)*

---

## Step 95: Login Error Messages

#### Next: Display an error when login fails

Remember this line in `accounts/views.py`?

```python
messages.error(request, 'Invalid username or password.')
```

It creates an error message, but our HTML page must display it.

Open **`templates/accounts/login.html`**.

If it isn't already present, add this code just above the login `<form>`:

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

#### How it works

- `messages` — contains messages Django has prepared for the page.
- `{% for message in messages %}` — goes through those messages one by one.
- `{{ message }}` — displays each message.

**Test:** Enter an incorrect password on the login page.

Expected output:

  Invalid username or password.

This helps users understand why they weren't logged in.

*Previous: Step 94 (Admin Access Verified)  |  Next: Step 96 (Password Change Confirmation)*

---

## Step 96: Password Change Confirmation

#### Next: Show a success message after changing the password

Currently, when a user changes their password successfully, Django redirects them to the homepage without any confirmation.

Open **`accounts/views.py`**.

Find this section inside `changepassword()`:

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    return redirect('home')
```

Add one line before the redirect:

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    messages.success(request, 'Password changed successfully.')
    return redirect('home')
```

#### What does the new line do?

`messages.success()` tells Django to prepare a confirmation message for the user.

- `request` — identifies the current user's request.
- `'Password changed successfully.'` — the message to display.

**Expected result:** Save the file without errors.

Next, we'll make the homepage display this message.

*Previous: Step 95 (Login Error Messages)  |  Next: Step 97 (Success Message)*

---

## Step 97: Success Message

#### Next: Display the password-change success message

Open **`core/templates/core/home.html`**.

Immediately below the `<body>` tag, add:

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

#### What happens now?

1. The user changes their password.
2. `messages.success()` creates a confirmation message.
3. Django redirects the user to the homepage.
4. The code above displays the message.

**Test:** Change your password successfully.

You should return to the homepage and see:

    Password changed successfully.

We'll style these messages properly during the UI design phase.

#### How we checked it

#### Next: Check Module 1 for Django configuration errors

We've implemented and tested login, logout, password changes, and role display.

Now open your **VS Code terminal** in the folder containing `manage.py`.

Run:

```bash
python manage.py check
```

**What does this do?**

Django examines your project configuration, models, URLs, and other registered components for common errors.

**Expected output:**

```text
System check identified no issues (0 silenced).
```

This doesn't test every feature, but it helps confirm that the project configuration is valid.

We're nearing the end of Module 1. We'll enforce role-specific permissions on inventory operations when we create those operations in subsequent modules.

#### How we checked it

#### Next: Check which files have changed

Before saving Module 1 to GitHub, let's review the files Git has detected.

Open your VS Code terminal in the folder containing `manage.py`.

Run:

```bash
git status --short
```

**What does this command do?**

It lists files that have been added, modified, or deleted since your last commit.

For example:

```text
 M config/settings.py
 M config/urls.py
 M core/views.py
?? accounts/
```

- `M` means an existing file was modified.
- `??` means Git hasn't started tracking that file or folder yet.

**Important:** We should not commit `db.sqlite3`, `.venv`, or password-containing files.

**Expected result:** A list of changed files. Send me the terminal output so we can review it before committing.

#### How we checked it

Your Git status looks as expected.

| Status | Files | Meaning |
|---|---|---|
| Modified | `config/settings.py`, `config/urls.py` | Project configuration changed |
| Modified | `core/views.py`, `core/templates/core/home.html` | Homepage updated |
| New | `accounts/` | Accounts app created |
| New | `templates/` | Login and password-change templates |

**Before committing**, let's check exactly which files are inside those new folders.

Run:

```bash
git status --short --untracked-files=all
```

This lists individual files instead of showing only `accounts/` and `templates/`.

**Send me the output.** We'll check that no unnecessary files are included before committing Module 1.

*Previous: Step 96 (Password Change Confirmation)  |  Next: Step 98 (Stage Files)*

---

## Step 98: Stage Files

**All 12 changed or new files look appropriate for Module 1.**

- `accounts/migrations/0001_initial.py` — should be committed because it defines our database structure.
- `accounts/tests.py` — Django's default test file; fine to include.
- No database, virtual environment, or password files appear in the list.

#### Next: Stage the files for Git

Run:

```bash
git add config/settings.py config/urls.py core/views.py core/templates/core/home.html accounts/ templates/accounts/
```

**What does `git add` do?**

It tells Git which changes to include in the next commit.

It does **not** upload anything to GitHub yet.

**Expected result:** The command finishes without displaying an error.

*Previous: Step 97 (Success Message)  |  Next: Step 99 (Commit Changes)*

---

## Step 99: Commit Changes

#### Next: Save Module 1 in Git

Your files are now staged. Let's create a commit.

Run this in your VS Code terminal:

```bash
git commit -m "Complete Module 1 accounts and authentication"
```

**What does this do?**

A commit saves a snapshot of your staged changes in your local Git repository.

**Expected output:** Something similar to:

```text
[main abc1234] Complete Module 1 accounts and authentication
12 files changed, ...
```

The commit ID and file counts may differ.

This saves the work **locally**. We will push it to GitHub next.

*Previous: Step 98 (Stage Files)  |  Next: Step 100 (Role Security)*

---
