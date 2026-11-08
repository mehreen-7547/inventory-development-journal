# MODULE 1 — Employee accounts

## Chapter 7: Role-based homepage and feedback messages (Steps 91–99)

### Before we begin

The homepage and feedback messages were working, but people with different jobs were seeing similar information.

### What we worked on

We made the homepage look different by role and showed useful login/password messages. We then saved the code changes in Git.

### Picture it this way

This chapter is about what the user sees. Proper server-side protection for restricted pages is a different matter, and we build it in the next chapter.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 91: Role-Based Homepage](#step-91)
- [Step 92: Admin Link Visibility](#step-92)
- [Step 93: Why hiding a link does not secure Django Admin](#step-93)
- [Step 94: Verify manager access is restricted](#step-94)
- [Step 95: Login Error Messages](#step-95)
- [Step 96: Password Change Confirmation](#step-96)
- [Step 97: Success Message](#step-97)
- [Step 98: Stage Files](#step-98)
- [Step 99: Commit Changes](#step-99)

---

<a id="step-91"></a>

### Step 91: Role-Based Homepage

**What was happening before this step?**

The same homepage was being shown to people with different jobs. We wanted the text and options to reflect their role.

**What did we do, and what does it mean?**

The template checks the signed-in user's role and shows suitable labels or sections. For example, an administrator should see account-management options that sales staff do not need.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Below the welcome message, add.*

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

**Understanding the code, one piece at a time**

A Django template can ask whether the current person's role is `admin`, `manager` or `sales`, and then show different headings or links. This changes the page the visitor sees, but does not replace permission checks in Python views.

**How did we know what happened?**

Review the home display under each role; access control is implemented separately in Steps 100–107.

**Where to go next:** Previous: [Step 90 — Change Password Link](06-password-change.md#step-90) · Next: [Step 92 — Admin Link Visibility](07-role-based-homepage-and-feedback-messages.md#step-92)


---

<a id="step-92"></a>

### Step 92: Admin Link Visibility

**What was happening before this step?**

The administrator link should not tempt a manager or sales employee to open a page they cannot use.

**What did we do, and what does it mean?**

The template can hide a link when the current person's role is not Admin. This improves the interface, but hiding a link is not a security check; the server must still protect the target page.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Replace.*

```html
<p><a href="/admin/">Open Django admin</a></p>
```

*2. With.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How did we know what happened?**

The next two entries clarify and verify that Django Admin already rejects unauthorized accounts.

**Where to go next:** Previous: [Step 91 — Role-Based Homepage](07-role-based-homepage-and-feedback-messages.md#step-91) · Next: [Step 93 — Why hiding a link does not secure Django Admin](07-role-based-homepage-and-feedback-messages.md#step-93)


---

<a id="step-93"></a>

### Step 93: Why hiding a link does not secure Django Admin

**What was happening before this step?**

We had hidden admin-related navigation from other roles. We needed to make sure nobody thought that meant Django Admin was now protected by our custom template rule.

**What did we do, and what does it mean?**

Django Admin has its own permission checks. A user could type its URL directly, so access must be checked on the server. Menu visibility and permission are two separate things.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. So what did our code actually change?.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How did we know what happened?**

The recorded explanation says `manager1` has `is_staff=False`; a direct URL test is discussed next.

**Where to go next:** Previous: [Step 92 — Admin Link Visibility](07-role-based-homepage-and-feedback-messages.md#step-92) · Next: [Step 94 — Verify manager access is restricted](07-role-based-homepage-and-feedback-messages.md#step-94)


---

<a id="step-94"></a>

### Step 94: Verify manager access is restricted

**What was happening before this step?**

We tested or reviewed what happens when a manager attempts to reach an admin-only location.

**What did we do, and what does it mean?**

The intended result is that an ordinary manager cannot get administrator privileges merely by knowing the URL. This step checked access behaviour, not just what the homepage looks like.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. One thing remains: your screenshot still shows that link for manager1. Make sure you have saved this change in home.html.*

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

**How did we know what happened?**

Our notes describes the manager's denied Admin visit as the expected result.

**Where to go next:** Previous: [Step 93 — Why hiding a link does not secure Django Admin](07-role-based-homepage-and-feedback-messages.md#step-93) · Next: [Step 95 — Login Error Messages](07-role-based-homepage-and-feedback-messages.md#step-95)


---

<a id="step-95"></a>

### Step 95: Login Error Messages

**What was happening before this step?**

A wrong username or password needs an understandable message, otherwise users do not know what happened.

**What did we do, and what does it mean?**

The login template displays the error or feedback set by the view. It does not reveal the correct password or tell a user how to bypass authentication.

**File or place:** `templates/accounts/login.html`.

**Code or command from this step**

*1. Remember this line in accounts/views.py?.*

```python
messages.error(request, 'Invalid username or password.')
```

*2. If it isn't already present, add this code just above the login <form>.*

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

Try a wrong password and verify an error appears without logging in. No separate automated check was preserved.

**Where to go next:** Previous: [Step 94 — Verify manager access is restricted](07-role-based-homepage-and-feedback-messages.md#step-94) · Next: [Step 96 — Password Change Confirmation](07-role-based-homepage-and-feedback-messages.md#step-96)


---

<a id="step-96"></a>

### Step 96: Password Change Confirmation

**What was happening before this step?**

After a successful password change, the user needed an indication that the update worked.

**What did we do, and what does it mean?**

The view sets a success message so the next page can show confirmation. That message is separate from saving the new password: the database change and the visual feedback have different jobs.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Find this section inside changepassword().*

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    return redirect('home')
```

*2. Add one line before the redirect.*

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    messages.success(request, 'Password changed successfully.')
    return redirect('home')
```

**Understanding the code, one piece at a time**

`redirect(...)` does not display a page itself. It tells the browser which page to open next, often after a form has been saved.

**How did we know what happened?**

The message is only visible after the homepage template displays messages in Step 97.

**Where to go next:** Previous: [Step 95 — Login Error Messages](07-role-based-homepage-and-feedback-messages.md#step-95) · Next: [Step 97 — Success Message](07-role-based-homepage-and-feedback-messages.md#step-97)


---

<a id="step-97"></a>

### Step 97: Success Message

**What was happening before this step?**

We had a message ready to display but needed the homepage template to show it.

**What did we do, and what does it mean?**

Django messages are short notices saved between a redirect and the next page. The template reads them and places them where the user can see them.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Immediately below the <body> tag, add.*

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

**Understanding the code, one piece at a time**

`{% for ... %}` repeats the HTML inside it for every item in a list, such as one table row for each employee.

**How did we know what happened?**

Change a password, return home, and check for the confirmation. This is a suggested functional check, not a stored test log.

**Where to go next:** Previous: [Step 96 — Password Change Confirmation](07-role-based-homepage-and-feedback-messages.md#step-96) · Next: [Step 98 — Stage Files](07-role-based-homepage-and-feedback-messages.md#step-98)


---

<a id="step-98"></a>

### Step 98: Stage Files

**What was happening before this step?**

Several account pages and messages had changed, and we wanted to record that working point.

**What did we do, and what does it mean?**

`git add` selected those application files for the next snapshot. It did not run tests or upload them.

**File or place:** `application Git staging`.

**Code or command from this step**

*1. Run.*

```bash
git add config/settings.py config/urls.py core/views.py core/templates/core/home.html accounts/ templates/accounts/
```

**How did we know what happened?**

The recorded note says twelve changed/new files were appropriate for this module.

**Where to go next:** Previous: [Step 97 — Success Message](07-role-based-homepage-and-feedback-messages.md#step-97) · Next: [Step 99 — Commit Changes](07-role-based-homepage-and-feedback-messages.md#step-99)


---

<a id="step-99"></a>

### Step 99: Commit Changes

**What was happening before this step?**

After staging the changes, we saved them in the project's Git history.

**What did we do, and what does it mean?**

The commit makes this version easy to find later. It is a local record of the code at that point, not a guarantee that every page has been tested.

**File or place:** `application Git commit`.

**Code or command from this step**

*1. Run this in your VS Code terminal.*

```bash
git commit -m "Complete Module 1 accounts and authentication"
```

*2. Example result from the notes (not a command to run).*

```text
[main abc1234] Complete Module 1 accounts and authentication
12 files changed, ...
```

**How did we know what happened?**

No new runtime feature is introduced by this commit.

**Where to go next:** Previous: [Step 98 — Stage Files](07-role-based-homepage-and-feedback-messages.md#step-98) · Next: [Step 100 — Role Security](08-role-security-decorators.md#step-100)

---

### The point of this chapter

This chapter is about what the user sees. Proper server-side protection for restricted pages is a different matter, and we build it in the next chapter.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

