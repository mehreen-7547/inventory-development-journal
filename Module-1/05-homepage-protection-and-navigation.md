# MODULE 1 — Accounts

## Chapter 5: Homepage protection and navigation (Steps 83–85)

**Where we are.** The homepage initially lacked protection and did not identify the current user. We closed both gaps.

**What this chapter gives us.** Restrict anonymous access, show the user's identity, and redirect signed-in users away from login.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain what request.user and login_required do.

### Find a step

- [Step 83: Protect Homepage](#step-83)
- [Step 84: Display User Details](#step-84)
- [Step 85: Login Page Check](#step-85)

---

<a id="step-83"></a>

### Step 83: Protect Homepage

**Why we are doing this.** Even with a working login page, the homepage was publicly accessible unless its view enforced authentication.

**File / location.** `core/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open core/views.py and update it.*

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login/')
def home(request):
    return render(request, 'core/home.html')
```

**How it works.** `@login_required` wraps the home view, redirecting anonymous visitors to the login page. Hiding homepage links alone would not secure the route.

**Follow the flow.** `@login_required` executes before the home view. If the browser has no authenticated session, Django redirects to `/login/`; if it does, `home` renders the template. A template link is not security; this check lives in the server-side view.

**How we checked it.** A direct visit to `/` while logged out should redirect to login; no independent test trace was preserved.

**Connects to.** Previous: [Step 82 — Logout Button](04-logout.md#step-82) · Next: [Step 84 — Display User Details](05-homepage-protection-and-navigation.md#step-84)


---

<a id="step-84"></a>

### Step 84: Display User Details

**Why we are doing this.** After securing the homepage, we made it clear which account was currently signed in.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Above the Logout button, add.*

```html
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>
```

**How it works.** `request.user` comes from Django's session authentication. The template displays the username and human-readable role from the model's field choices.

**How we checked it.** Verify by logging in with accounts of different roles and checking the displayed details.

**Connects to.** Previous: [Step 83 — Protect Homepage](05-homepage-protection-and-navigation.md#step-83) · Next: [Step 85 — Login Page Check](05-homepage-protection-and-navigation.md#step-85)


---

<a id="step-85"></a>

### Step 85: Login Page Check

**Why we are doing this.** An already signed-in user should not be sent back to a login form unnecessarily.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add these two lines at the beginning of loginpage(), before the existing POST check.*

```python
def loginpage(request):
    if request.user.is_authenticated:
        return redirect('home')
```

**How it works.** `request.user.is_authenticated` detects an active session; the view redirects the user to home before rendering the login template.

**How we checked it.** A logged-in browser should open `/login/` and land on the homepage. This is the expected behavior, not a documented automated test.

**Connects to.** Previous: [Step 84 — Display User Details](05-homepage-protection-and-navigation.md#step-84) · Next: [Step 86 — Change Password](06-password-change.md#step-86)

---

**Chapter recap.** Restrict anonymous access, show the user's identity, and redirect signed-in users away from login.

**Practice explaining:** Explain what request.user and login_required do.

