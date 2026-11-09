# MODULE 1 — Employee accounts

## Chapter 7: Role-based homepage and feedback messages (Steps 91–99)

### Before we begin

The homepage and feedback messages were working, but people with different jobs were seeing similar information.

### What we worked on

We made the homepage look different by role and showed useful login/password messages. We then saved the code changes in Git.

### Picture it this way

This chapter is about what the user sees. Proper server-side protection for restricted pages is a different matter, and we build it in the next chapter.

### Find a step

- [Step 91: Role-Based Homepage](#step-91)
- [Step 92: Admin Link Visibility](#step-92)
- [Step 93: Why hiding a link does not secure Django Admin](#step-93)
- [Step 94: Verify manager access is restricted](#step-94)
- [Step 95: Login Error Messages](#step-95)
- [Step 96: Password Change Confirmation](#step-96)
- [Step 97: Success Message](#step-97)

---

<a id="step-91"></a>

### Step 91: Role-Based Homepage

#### Before this step

The same homepage was being shown to people with different jobs. We wanted the text and options to reflect their role. The preceding step, Step 90, covered change password link, so this was the next part of the development sequence.

#### What we did and why

The template checks the signed-in user's role and shows suitable labels or sections. For example, an administrator should see account-management options that sales staff do not need.

#### Code and commands

**Step 91, block 1.** This code sample or terminal command was used while working on role-based homepage. The content below remains in its original sequence for Step 91, block 1.

```html
{% if request.user.role == 'admin' %}
    <p>Administrator Dashboard</p>

{% elif request.user.role == 'manager' %}
    <p>Inventory Manager Dashboard</p>

{% elif request.user.role == 'sales' %}
    <p>Sales Staff Dashboard</p>
{% endif %}
```

#### How the code works

**Block 1 (html, 9 lines).** In Step 91, role-based homepage involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% if request.user.role == 'admin' %}` decides which HTML is included based on the value Django provided. The HTML element `<p>Administrator Dashboard</p>` creates or closes part of the page structure seen by the browser. `{% elif request.user.role == 'manager' %}` decides which HTML is included based on the value Django provided. The HTML element `<p>Inventory Manager Dashboard</p>` creates or closes part of the page structure seen by the browser.

**Lines 8–9:** The HTML element `<p>Sales Staff Dashboard</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 91. For Step 91, the proposed way to check role-based homepage was to open the related page in the browser and check that the route reached the intended view or template in `core/templates/core/home.html`. Development continued in Step 92 before a complete result could be claimed.

#### Next

[Step 90: Change Password Link](../Module-1/06-password-change.md#step-90) · [Step 92: Admin Link Visibility](#step-92)


---

<a id="step-92"></a>

### Step 92: Admin Link Visibility

#### Before this step

The administrator link should not tempt a manager or sales employee to open a page they cannot use. Immediately before this, Step 91 dealt with role-based homepage; the work here continued the same part of the application.

#### What we did and why

The template can hide a link when the current person's role is not Admin. This improves the interface, but hiding a link is not a security check; the server must still protect the target page.

#### Code and commands

**Step 92, block 1.** This code sample or terminal command was used while working on admin link visibility. The content below remains in its original sequence for Step 92, block 1.

```html
<p><a href="/admin/">Open Django admin</a></p>
```

**Step 92, block 2.** This code sample or terminal command was used while working on admin link visibility. The content below remains in its original sequence for Step 92, block 2.

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

#### How the code works

**Block 1 (html, 1 lines).** In Step 92, admin link visibility involved `core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The link `<p><a href="/admin/">Open Django admin</a></p>` lets the user navigate to the named screen from this page.

**Block 2 (html, 3 lines).** In Step 92, admin link visibility involved `core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `{% if request.user.is_staff %}` decides which HTML is included based on the value Django provided. The link `<p><a href="/admin/">Open Django admin</a></p>` lets the user navigate to the named screen from this page.

#### How we checked it

Not tested yet at Step 92. For Step 92, the proposed way to check admin link visibility was to revisit `core/home.html` and run the corresponding command or feature to see its result. Development continued in Step 93 before a complete result could be claimed.

#### Next

[Step 91: Role-Based Homepage](#step-91) · [Step 93: Why hiding a link does not secure Django Admin](#step-93)


---

<a id="step-93"></a>

### Step 93: Why hiding a link does not secure Django Admin

#### Before this step

We had hidden admin-related navigation from other roles. We needed to make sure nobody thought that meant Django Admin was now protected by our custom template rule. Immediately before this, Step 92 dealt with admin link visibility; the work here continued the same part of the application.

#### What we did and why

Django Admin has its own permission checks. A user could type its URL directly, so access must be checked on the server. Menu visibility and permission are two separate things.

#### Code and commands

**Step 93, block 1.** This code sample or terminal command was used while working on why hiding a link does not secure django admin. The content below remains in its original sequence for Step 93, block 1.

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

#### How the code works

**Block 1 (html, 3 lines).** In Step 93, why hiding a link does not secure django admin involved `home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `{% if request.user.is_staff %}` decides which HTML is included based on the value Django provided. The link `<p><a href="/admin/">Open Django admin</a></p>` lets the user navigate to the named screen from this page.

#### How we checked it

Not tested yet at Step 93. For Step 93, the proposed way to check why hiding a link does not secure django admin was to revisit `home.html` and run the corresponding command or feature to see its result. Development continued in Step 94 before a complete result could be claimed.

#### Next

[Step 92: Admin Link Visibility](#step-92) · [Step 94: Verify manager access is restricted](#step-94)


---

<a id="step-94"></a>

### Step 94: Verify manager access is restricted

#### Before this step

We tested or reviewed what happens when a manager attempts to reach an admin-only location. Immediately before this, Step 93 dealt with why hiding a link does not secure django admin; the work here continued the same part of the application.

#### What we did and why

The intended result is that an ordinary manager cannot get administrator privileges merely by knowing the URL. This step checked access behaviour, not just what the homepage looks like.

#### Code and commands

**Step 94, block 1.** This code sample or terminal command was used while working on verify manager access is restricted. The content below remains in its original sequence for Step 94, block 1.

```html
{% if request.user.is_staff %}
    <p><a href="/admin/">Open Django admin</a></p>
{% endif %}
```

#### How the code works

**Block 1 (html, 3 lines).** In Step 94, verify manager access is restricted involved `home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `{% if request.user.is_staff %}` decides which HTML is included based on the value Django provided. The link `<p><a href="/admin/">Open Django admin</a></p>` lets the user navigate to the named screen from this page.

#### How we checked it

Not tested yet at Step 94. For Step 94, the proposed way to check verify manager access is restricted was to revisit `home.html` and run the corresponding command or feature to see its result. Development continued in Step 95 before a complete result could be claimed.

#### Next

[Step 93: Why hiding a link does not secure Django Admin](#step-93) · [Step 95: Login Error Messages](#step-95)


---

<a id="step-95"></a>

### Step 95: Login Error Messages

#### Before this step

A wrong username or password needs an understandable message, otherwise users do not know what happened. Immediately before this, Step 94 dealt with verify manager access is restricted; the work here continued the same part of the application.

#### What we did and why

The login template displays the error or feedback set by the view. It does not reveal the correct password or tell a user how to bypass authentication.

#### Code and commands

**Step 95, block 1.** This code sample or terminal command was used while working on login error messages. The content below remains in its original sequence for Step 95, block 1.

```python
messages.error(request, 'Invalid username or password.')
```

**Step 95, block 2.** This code sample or terminal command was used while working on login error messages. The content below remains in its original sequence for Step 95, block 2.

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 95, login error messages involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `messages.error(request, 'Invalid username or password.')` prepares feedback that can appear when the user reaches the next page.

**Block 2 (html, 3 lines).** In Step 95, login error messages involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p>{{ message }}</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 95. For Step 95, the proposed way to check login error messages was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 96 before a complete result could be claimed.

#### Next

[Step 94: Verify manager access is restricted](#step-94) · [Step 96: Password Change Confirmation](#step-96)


---

<a id="step-96"></a>

### Step 96: Password Change Confirmation

#### Before this step

After a successful password change, the user needed an indication that the update worked. Immediately before this, Step 95 dealt with login error messages; the work here continued the same part of the application.

#### What we did and why

The view sets a success message so the next page can show confirmation. That message is separate from saving the new password: the database change and the visual feedback have different jobs.

#### Code and commands

**Step 96, block 1.** This code sample or terminal command was used while working on password change confirmation. The content below remains in its original sequence for Step 96, block 1.

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    return redirect('home')
```

**Step 96, block 2.** This code sample or terminal command was used while working on password change confirmation. The content below remains in its original sequence for Step 96, block 2.

```python
if form.is_valid():
    user = form.save()
    update_session_auth_hash(request, user)
    messages.success(request, 'Password changed successfully.')
    return redirect('home')
```

#### How the code works

**Block 1 (python, 4 lines).** In Step 96, password change confirmation involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–4:** `if form.is_valid():` is a condition: the code inside this branch only runs when its condition is satisfied. `form.save()` writes the validated form values into the corresponding database record. `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

**Block 2 (python, 5 lines).** In Step 96, password change confirmation involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `if form.is_valid():` is a condition: the code inside this branch only runs when its condition is satisfied. `form.save()` writes the validated form values into the corresponding database record. `messages.success(request, 'Password changed successfully.')` prepares feedback that can appear when the user reaches the next page. `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 96. For Step 96, the proposed way to check password change confirmation was to open the password-related page and try the expected form flow with a test account. Development continued in Step 97 before a complete result could be claimed.

#### Next

[Step 95: Login Error Messages](#step-95) · [Step 97: Success Message](#step-97)


---

<a id="step-97"></a>

### Step 97: Success Message

#### Before this step

We had a message ready to display but needed the homepage template to show it. Immediately before this, Step 96 dealt with password change confirmation; the work here continued the same part of the application.

#### What we did and why

Django messages are short notices saved between a redirect and the next page. The template reads them and places them where the user can see them.

#### Code and commands

**Step 97, block 1.** This code sample or terminal command was used while working on success message. The content below remains in its original sequence for Step 97, block 1.

```html
{% for message in messages %}
    <p>{{ message }}</p>
{% endfor %}
```

**Step 97, block 2.** This code sample or terminal command was used while working on success message. The content below remains in its original sequence for Step 97, block 2.

```bash
python manage.py check
```

**Step 97, block 3.** This output was used while working on success message. The content below remains in its original sequence for Step 97, block 3.

```text
System check identified no issues (0 silenced).
```

**Step 97, block 4.** This code sample or terminal command was used while working on success message. The content below remains in its original sequence for Step 97, block 4.

```bash
git status --short
```

**Step 97, block 5.** This output was used while working on success message. The content below remains in its original sequence for Step 97, block 5.

```text
 M config/settings.py
 M config/urls.py
 M core/views.py
?? accounts/
```

**Step 97, block 6.** This code sample or terminal command was used while working on success message. The content below remains in its original sequence for Step 97, block 6.

```bash
git status --short --untracked-files=all
```

#### How the code works

**Block 1 (html, 3 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p>{{ message }}</p>` creates or closes part of the page structure seen by the browser.

**Block 2 (bash, 1 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py check` starts Python or one of its tools from the development terminal.

**Block 3 (text, 1 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 97, output block 3 showed the result rather than another command. Its visible value was `System check identified no issues (0 silenced).`, which mattered for success message.

**Block 4 (bash, 1 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git status --short` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 5 (text, 4 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 97, output block 5 showed the result rather than another command. Its visible value was ` M config/settings.py`, which mattered for success message.

**Block 6 (bash, 1 lines).** In Step 97, success message involved `core/templates/core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git status --short --untracked-files=all` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

Not tested yet at Step 97. For Step 97, the proposed way to check success message was to revisit `core/templates/core/home.html` and run the corresponding command or feature to see its result. Development continued in Step 98 before a complete result could be claimed.

#### Next

[Step 96: Password Change Confirmation](#step-96) · [Step 98: Stage Files](#step-98)
