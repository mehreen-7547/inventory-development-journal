# MODULE 1 — Employee accounts

## Chapter 5: Homepage protection and navigation (Steps 83–85)

### Before we begin

After login was working, we noticed the homepage still needed proper protection and clearer information about the current user.

### What we worked on

We required login for the homepage, showed who was signed in and reviewed what happens when someone already logged in opens the login page.

### Picture it this way

The main lesson is that protecting a page happens in its Django view. Hiding a menu item does not stop someone typing the page's address directly.

### Find a step

- [Step 83: Protect Homepage](#step-83)
- [Step 84: Display User Details](#step-84)
- [Step 85: Login Page Check](#step-85)

---

<a id="step-83"></a>

### Step 83: Protect Homepage

#### Before this step

The login page worked, but someone could still type the homepage address directly without signing in. The preceding step, Step 82, covered logout button, so this was the next part of the development sequence.

#### What we did and why

login_required checks for a signed-in user before allowing the homepage view to run. It closes the shortcut around the login screen: hiding a link would not be enough.

#### Code and commands

**Step 83, block 1.** This code sample or terminal command was used while working on protect homepage. The content below remains in its original sequence for Step 83, block 1.

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required

@login_required(login_url='/login/')
def home(request):
    return render(request, 'core/home.html')
```

#### How the code works

**Block 1 (python, 6 lines).** In Step 83, protect homepage involved `core/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–6:** `from django.shortcuts import render` makes a Python function, class or module available to the code that follows. `from django.contrib.auth.decorators import login_required` makes a Python function, class or module available to the code that follows. `@login_required(login_url='/login/')` adds a check or behaviour around the view before Django runs the view itself. `def home(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

#### How we checked it

Not tested yet at Step 83. For Step 83, the proposed way to check protect homepage was to open the related page in the browser and check that the route reached the intended view or template in `core/views.py`. Development continued in Step 84 before a complete result could be claimed.

#### Next

[Step 82: Logout Button](../Module-1/04-logout.md#step-82) · [Step 84: Display User Details](#step-84)


---

<a id="step-84"></a>

### Step 84: Display User Details

#### Before this step

Now that only signed-in users could see the homepage, we wanted them to know whose account was open. Immediately before this, Step 83 dealt with protect homepage; the work here continued the same part of the application.

#### What we did and why

The template displays information from request.user, which is Django's record of the current visitor after login. It is not a list of all employees; it is the currently signed-in person.

#### Code and commands

**Step 84, block 1.** This code sample or terminal command was used while working on display user details. The content below remains in its original sequence for Step 84, block 1.

```html
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>
```

#### How the code works

**Block 1 (html, 2 lines).** In Step 84, display user details involved `core/home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The HTML element `<h2>Welcome, {{ request.user.username }}</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Role: {{ request.user.get_role_display }}</p>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 84. For Step 84, the proposed way to check display user details was to revisit `core/home.html` and run the corresponding command or feature to see its result. Development continued in Step 85 before a complete result could be claimed.

#### Next

[Step 83: Protect Homepage](#step-83) · [Step 85: Login Page Check](#step-85)


---

<a id="step-85"></a>

### Step 85: Login Page Check

#### Before this step

A person who is already signed in does not need to see the login form again. Immediately before this, Step 84 dealt with display user details; the work here continued the same part of the application.

#### What we did and why

We checked the login behaviour and adjusted or confirmed the redirect so an authenticated user reaches the appropriate page. This avoids presenting an unnecessary username/password form to a current user.

#### Code and commands

**Step 85, block 1.** This code sample or terminal command was used while working on login page check. The content below remains in its original sequence for Step 85, block 1.

```python
def loginpage(request):
    if request.user.is_authenticated:
        return redirect('home')
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 85, login page check involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `def loginpage(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `if request.user.is_authenticated:` is a condition: the code inside this branch only runs when its condition is satisfied. `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 85. For Step 85, the proposed way to check login page check was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Step 85 did not establish a successful outcome for login page check.

#### Next

[Step 84: Display User Details](#step-84) · [Step 86: Change Password](../Module-1/06-password-change.md#step-86)


### Check your understanding

Without looking at the code, explain why **homepage protection and navigation** was needed at this point, and describe what the application did differently after the final step of this chapter.
