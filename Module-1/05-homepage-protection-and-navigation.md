# MODULE 1 — Employee accounts

## Chapter 5: Homepage protection and navigation (Steps 83–85)

### Before we begin

After login was working, we noticed the homepage still needed proper protection and clearer information about the current user.

### What we worked on

We required login for the homepage, showed who was signed in and reviewed what happens when someone already logged in opens the login page.

### Picture it this way

The main lesson is that protecting a page happens in its Django view. Hiding a menu item does not stop someone typing the page's address directly.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 83: Protect Homepage](#step-83)
- [Step 84: Display User Details](#step-84)
- [Step 85: Login Page Check](#step-85)

---

<a id="step-83"></a>

### Step 83: Protect Homepage

**What was happening before this step?**

The login page worked, but someone could still type the homepage address directly without signing in.

**What did we do, and what does it mean?**

`login_required` checks for a signed-in user before allowing the homepage view to run. It closes the shortcut around the login screen: hiding a link would not be enough.

**File or place:** `core/views.py`.

**Code or command from this step**

*1. Open core/views.py and update it.*

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login/')
def home(request):
    return render(request, 'core/home.html')
```

**Understanding the code, one piece at a time**

`@login_required` is written just above a view. Django runs that check before the view, so an unsigned visitor cannot open a protected page simply by typing its address.

**How did we know what happened?**

A direct visit to `/` while logged out should redirect to login; no independent test trace was preserved.

**Where to go next:** Previous: [Step 82 — Logout Button](04-logout.md#step-82) · Next: [Step 84 — Display User Details](05-homepage-protection-and-navigation.md#step-84)


---

<a id="step-84"></a>

### Step 84: Display User Details

**What was happening before this step?**

Now that only signed-in users could see the homepage, we wanted them to know whose account was open.

**What did we do, and what does it mean?**

The template displays information from `request.user`, which is Django's record of the current visitor after login. It is not a list of all employees; it is the currently signed-in person.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Above the Logout button, add.*

```html
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>
```

**How did we know what happened?**

Verify by logging in with accounts of different roles and checking the displayed details.

**Where to go next:** Previous: [Step 83 — Protect Homepage](05-homepage-protection-and-navigation.md#step-83) · Next: [Step 85 — Login Page Check](05-homepage-protection-and-navigation.md#step-85)


---

<a id="step-85"></a>

### Step 85: Login Page Check

**What was happening before this step?**

A person who is already signed in does not need to see the login form again.

**What did we do, and what does it mean?**

We checked the login behaviour and adjusted or confirmed the redirect so an authenticated user reaches the appropriate page. This avoids presenting an unnecessary username/password form to a current user.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Add these two lines at the beginning of loginpage(), before the existing POST check.*

```python
def loginpage(request):
    if request.user.is_authenticated:
        return redirect('home')
```

**Understanding the code, one piece at a time**

`redirect(...)` does not display a page itself. It tells the browser which page to open next, often after a form has been saved.

**How did we know what happened?**

A logged-in browser should open `/login/` and land on the homepage. This is the expected behavior, not a documented automated test.

**Where to go next:** Previous: [Step 84 — Display User Details](05-homepage-protection-and-navigation.md#step-84) · Next: [Step 86 — Change Password](06-password-change.md#step-86)

---

### The point of this chapter

The main lesson is that protecting a page happens in its Django view. Hiding a menu item does not stop someone typing the page's address directly.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

