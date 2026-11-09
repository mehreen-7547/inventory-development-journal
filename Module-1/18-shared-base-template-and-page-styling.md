# MODULE 1 — Employee accounts

## Chapter 18: Shared base template and page styling (Steps 177–194)

### Before we begin

The login and employee pages had grown one by one. They worked, but many contained repeated headers, menus and form styling.

### What we worked on

We built a shared page layout, updated each account screen to use it and corrected where form styling belonged. At the end, we also handled a case where an employee account saves successfully but its welcome email fails.

### Picture it this way

The shared base.html template is a page frame reused everywhere. Each child page fills in only its own content. The last two steps are about making the app explain email failures accurately, not about changing the shared layout.

### Find a step

- [Step 177: Plan the shared layout](#step-177)
- [Step 178: Create the shared base.html template](#step-178)
- [Step 179: Dashboard Template Inheritance](#step-179)
- [Step 180: Update Login Page](#step-180)
- [Step 181: Update Change Password Page](#step-181)
- [Step 182: Update Forgot Password Page](#step-182)
- [Step 183: Update Reset Confirmation](#step-183)
- [Step 184: Update Set New Password Page](#step-184)
- [Step 185: Update Reset Complete Page](#step-185)
- [Step 186: Update Employee List](#step-186)
- [Step 187: Update Create Employee](#step-187)
- [Step 188: Fix Form Styling](#step-188)
- [Step 189: Add CSS classes to creation form widgets](#step-189)
- [Step 190: Correct Form Styling](#step-190)
- [Step 191: Consolidate both employee forms](#step-191)
- [Step 192: Update Edit Employee](#step-192)

---

<a id="step-177"></a>

### Step 177: Plan the shared layout

#### Before this step

We had reached the part of the employee accounts module dealing with plan the shared layout. This step followed the work immediately before it. The preceding step, Step 176, covered send a real email through gmail, so this was the next part of the development sequence.

#### What we did and why

We worked through plan the shared layout using the project files and commands shown below.

#### Code and commands

**Step 177, block 1.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 1.

```python
from django.core.exceptions import PermissionDenied
```

**Step 177, block 2.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 2.

```python
from django.contrib import messages
from django.shortcuts import redirect
```

**Step 177, block 3.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 3.

```python
if request.user.role not in roles:
    raise PermissionDenied
```

**Step 177, block 4.** This code sample or terminal command was used while working on plan the shared layout. The content below remains in its original sequence for Step 177, block 4.

```python
if request.user.role not in roles:
    messages.error(request, 'You do not have permission to access this page.')
    return redirect('home')
```

#### How the code works

**Block 1 (python, 1 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `from django.core.exceptions import PermissionDenied` makes a Python function, class or module available to the code that follows.

**Block 2 (python, 2 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `from django.contrib import messages` makes a Python function, class or module available to the code that follows. `from django.shortcuts import redirect` makes a Python function, class or module available to the code that follows.

**Block 3 (python, 2 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied.

**Block 4 (python, 3 lines).** In Step 177, plan the shared layout involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** `if request.user.role not in roles:` is a condition: the code inside this branch only runs when its condition is satisfied. `messages.error(request, 'You do not have permission to access this page.')` prepares feedback that can appear when the user reaches the next page. `return redirect('home')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 177. For Step 177, the proposed way to check plan the shared layout was to revisit `base.html` and run the corresponding command or feature to see its result. Development continued in Step 178 before a complete result could be claimed.

#### Next

[Step 176: Send a real email through Gmail](../Module-1/17-real-email-delivery-smtp.md#step-176) · [Step 178: Create the shared base.html template](#step-178)


---

<a id="step-178"></a>

### Step 178: Create the shared base.html template

#### Before this step

We had reached the part of the employee accounts module dealing with create the shared base.html template. This step followed the work immediately before it. Immediately before this, Step 177 dealt with plan the shared layout; the work here continued the same part of the application.

#### What we did and why

We worked through create the shared base.html template using the project files and commands shown below.

#### Code and commands

**Step 178, block 1.** This code sample or terminal command was used while working on create the shared base.html template. The content below remains in its original sequence for Step 178, block 1.

```html
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block title %}Inventory System{% endblock %}</title>

    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"
          rel="stylesheet">

    {% block extra_css %}{% endblock %}
</head>
<body>

<nav class="navbar bg-body-tertiary border-bottom">
    <div class="container">
        <a class="navbar-brand" href="{% url 'home' %}">
            Inventory System
        </a>

        {% if user.is_authenticated %}
        <div class="d-flex align-items-center gap-3 flex-wrap">

            {% if user.role == 'admin' %}
            <a href="{% url 'employee_list' %}">
                Manage Employees
            </a>
            {% endif %}

            <a href="{% url 'change_password' %}">
                Change Password
            </a>

            <form method="post" action="{% url 'logout' %}">
                {% csrf_token %}
                <button type="submit" class="btn btn-outline-secondary btn-sm">
                    Logout
                </button>
            </form>

        </div>
        {% endif %}
    </div>
</nav>

<main class="container py-4">

    {% if messages %}
        {% for message in messages %}
            <div class="alert {% if 'error' in message.tags %}alert-danger{% elif 'success' in message.tags %}alert-success{% else %}alert-info{% endif %}">
                {{ message }}
            </div>
        {% endfor %}
    {% endif %}

    {% block content %}{% endblock %}

</main>

</body>
</html>
```

#### How the code works

**Block 1 (html, 63 lines).** In Step 178, create the shared base.html template involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html lang="en">` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<meta charset="UTF-8">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** `<title>{% block title %}Inventory System{% endblock %}</title>` marks the part of the page where a child template supplies its own content. The link `<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.8/dist/css/bootstrap.min.css"` lets the user navigate to the named screen from this page. `rel="stylesheet">` sets the named value used by the next operation in this file. `{% block extra_css %}{% endblock %}` marks the part of the page where a child template supplies its own content.

**Lines 15–21:** The HTML element `<body>` creates or closes part of the page structure seen by the browser. The HTML element `<nav class="navbar bg-body-tertiary border-bottom">` creates or closes part of the page structure seen by the browser. The HTML element `<div class="container">` creates or closes part of the page structure seen by the browser. `<a class="navbar-brand" href="{% url 'home' %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 22–28:** `{% if user.is_authenticated %}` decides which HTML is included based on the value Django provided. The HTML element `<div class="d-flex align-items-center gap-3 flex-wrap">` creates or closes part of the page structure seen by the browser. `{% if user.role == 'admin' %}` decides which HTML is included based on the value Django provided. `<a href="{% url 'employee_list' %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 29–35:** The HTML element `</a>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'change_password' %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 36–42:** `<form method="post" action="{% url 'logout' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. The HTML element `<button type="submit" class="btn btn-outline-secondary btn-sm">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser.

**Lines 43–49:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `</nav>` creates or closes part of the page structure seen by the browser. The HTML element `<main class="container py-4">` creates or closes part of the page structure seen by the browser.

**Lines 50–56:** `{% if messages %}` decides which HTML is included based on the value Django provided. `{% for message in messages %}` repeats the following operation once for each matching item. `<div class="alert {% if 'error' in message.tags %}alert-danger{% elif 'success' in message` decides which HTML is included based on the value Django provided. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 57–63:** `{% block content %}{% endblock %}` marks the part of the page where a child template supplies its own content. The HTML element `</main>` creates or closes part of the page structure seen by the browser. The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 178. For Step 178, the proposed way to check create the shared base.html template was to open the related page in the browser and check that the route reached the intended view or template in `base.html`. Development continued in Step 179 before a complete result could be claimed.

#### Next

[Step 177: Plan the shared layout](#step-177) · [Step 179: Dashboard Template Inheritance](#step-179)


---

<a id="step-179"></a>

### Step 179: Dashboard Template Inheritance

#### Before this step

Now that the common layout existed, the homepage needed to use it instead of repeating all its own HTML. Immediately before this, Step 178 dealt with create the shared base.html template; the work here continued the same part of the application.

#### What we did and why

The homepage template extends base.html and supplies its own heading and content inside named sections. Django combines both files into the final page shown in the browser.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Dashboard - Inventory System{% endblock %}`; Insert lines around `{% endblock %}`. For Step 179, this comparison used the code block from Step 163, which might not represent the whole file.

**Step 179, block 1.** This code sample or terminal command was used while working on dashboard template inheritance. The content below remains in its original sequence for Step 179, block 1.

```html
{% extends 'base.html' %}

{% block title %}Dashboard - Inventory System{% endblock %}

{% block content %}

<h1>Inventory &amp; Demand Intelligence</h1>

<!-- WELCOME -->
<h2>Welcome, {{ request.user.username }}</h2>
<p>Role: {{ request.user.get_role_display }}</p>

<!-- DASHBOARD -->
{% if request.user.role == 'admin' %}

    <h3>Administrator Dashboard</h3>

    {% if request.user.is_staff %}
        <p>
            <a href="/admin/">Open Django admin</a>
        </p>
    {% endif %}

{% elif request.user.role == 'manager' %}

    <h3>Inventory Manager Dashboard</h3>

{% elif request.user.role == 'sales' %}

    <h3>Sales Staff Dashboard</h3>

{% endif %}

{% endblock %}
```

**Step 179, block 2.** This code sample or terminal command was used while working on dashboard template inheritance. The content below remains in its original sequence for Step 179, block 2.

```powershell
python manage.py runserver
```

#### How the code works

**Block 1 (html, 34 lines).** In Step 179, dashboard template inheritance involved `home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Dashboard - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<h1>Inventory &amp; Demand Intelligence</h1>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The comment `<!-- WELCOME -->` explains the intention of the following section; it does not execute as application logic. The HTML element `<h2>Welcome, {{ request.user.username }}</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Role: {{ request.user.get_role_display }}</p>` creates or closes part of the page structure seen by the browser. The comment `<!-- DASHBOARD -->` explains the intention of the following section; it does not execute as application logic.

**Lines 15–21:** The HTML element `<h3>Administrator Dashboard</h3>` creates or closes part of the page structure seen by the browser. `{% if request.user.is_staff %}` decides which HTML is included based on the value Django provided. The HTML element `<p>` creates or closes part of the page structure seen by the browser. The link `<a href="/admin/">Open Django admin</a>` lets the user navigate to the named screen from this page.

**Lines 22–28:** `{% elif request.user.role == 'manager' %}` decides which HTML is included based on the value Django provided. The HTML element `<h3>Inventory Manager Dashboard</h3>` creates or closes part of the page structure seen by the browser. `{% elif request.user.role == 'sales' %}` decides which HTML is included based on the value Django provided.

**Lines 29–34:** The HTML element `<h3>Sales Staff Dashboard</h3>` creates or closes part of the page structure seen by the browser.

**Block 2 (powershell, 1 lines).** In Step 179, dashboard template inheritance involved `home.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python manage.py runserver` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 179. For Step 179, the proposed way to check dashboard template inheritance was to open the related page in the browser and check that the route reached the intended view or template in `home.html`. Development continued in Step 180 before a complete result could be claimed.

#### Next

[Step 178: Create the shared base.html template](#step-178) · [Step 180: Update Login Page](#step-180)


---

<a id="step-180"></a>

### Step 180: Update Login Page

#### Before this step

The login page still had its old standalone layout and looked different from the rest of the website. Immediately before this, Step 179 dealt with dashboard template inheritance; the work here continued the same part of the application.

#### What we did and why

We connected it to the shared base.html layout. The username/password form and login check remain the same; only the surrounding page structure and appearance changed.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% block title %}Login - Inventory System{% endblock %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-6 col-lg-4">`; Replace lines around `<h2 class="mb-4">Login</h2>`; Replace lines around `<form method="POST"> / {% csrf_token %}`; Replace lines around `<div class="mb-3"> / <label for="username" class="form-label">`; Replace lines around `<div class="mb-3"> / <label for="password" class="form-label">`. For Step 180, this comparison used the code block from Step 179, which might not represent the whole file.

**Step 180, block 1.** This code sample or terminal command was used while working on update login page. The content below remains in its original sequence for Step 180, block 1.

```html
{% extends 'base.html' %}

{% block title %}Login - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-4">

        <h2 class="mb-4">Login</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                <label for="username" class="form-label">
                    Username
                </label>
                <input type="text"
                       id="username"
                       name="username"
                       class="form-control"
                       placeholder="Enter username"
                       autocomplete="username"
                       required>
            </div>

            <div class="mb-3">
                <label for="password" class="form-label">
                    Password
                </label>
                <input type="password"
                       id="password"
                       name="password"
                       class="form-control"
                       placeholder="Enter password"
                       autocomplete="current-password"
                       required>
            </div>

            <button type="submit" class="btn btn-primary w-100">
                Login
            </button>
        </form>

        <p class="mt-3 text-center">
            <a href="{% url 'password_reset' %}">
                Forgot Password?
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 55 lines).** In Step 180, update login page involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Login - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-6 col-lg-4">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-4">Login</h2>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** The HTML element `<div class="mb-3">` creates or closes part of the page structure seen by the browser. The HTML element `<label for="username" class="form-label">` creates or closes part of the page structure seen by the browser. The HTML element `</label>` creates or closes part of the page structure seen by the browser. The HTML element `<input type="text"` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** `class="form-control"` sets the named value used by the next operation in this file. `placeholder="Enter username"` sets the named value used by the next operation in this file. `autocomplete="username"` sets the named value used by the next operation in this file. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** The HTML element `<label for="password" class="form-label">` creates or closes part of the page structure seen by the browser. The HTML element `</label>` creates or closes part of the page structure seen by the browser. The HTML element `<input type="password"` creates or closes part of the page structure seen by the browser. `id="password"` sets the named value used by the next operation in this file.

**Lines 36–42:** `placeholder="Enter password"` sets the named value used by the next operation in this file. `autocomplete="current-password"` sets the named value used by the next operation in this file. The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-primary w-100">` creates or closes part of the page structure seen by the browser.

**Lines 43–49:** The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser. The HTML element `<p class="mt-3 text-center">` creates or closes part of the page structure seen by the browser. `<a href="{% url 'password_reset' %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 50–55:** The HTML element `</p>` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 180. For Step 180, the proposed way to check update login page was to open the related page in the browser and check that the route reached the intended view or template in `base.html`. Development continued in Step 181 before a complete result could be claimed.

#### Next

[Step 179: Dashboard Template Inheritance](#step-179) · [Step 181: Update Change Password Page](#step-181)


---

<a id="step-181"></a>

### Step 181: Update Change Password Page

#### Before this step

We wanted the change-password page to look and behave like part of the same application. Immediately before this, Step 180 dealt with update login page; the work here continued the same part of the application.

#### What we did and why

The template now uses the shared layout, while its form still asks for old and new passwords. Moving HTML into the common design does not remove the password checks in the view.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Change Password - Inventory System{% endblock %}`; Replace lines around `{% block content %}`. For Step 181, this comparison used the code block from Step 89, which might not represent the whole file.

**Step 181, block 1.** This code sample or terminal command was used while working on update change password page. The content below remains in its original sequence for Step 181, block 1.

```html
{% extends 'base.html' %}

{% block title %}Change Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-4">Change Password</h2>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Change Password
            </button>

            <a href="{% url 'home' %}"
               class="btn btn-outline-secondary">
                Cancel
            </a>
        </form>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 32 lines).** In Step 181, update change password page involved `change_password.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Change Password - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-6 col-lg-5">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-4">Change Password</h2>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** The HTML element `<div class="mb-3">` creates or closes part of the page structure seen by the browser. `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-primary">` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** `<a href="{% url 'home' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-secondary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Lines 29–32:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 181. For Step 181, the proposed way to check update change password page was to open the related page in the browser and check that the route reached the intended view or template in `change_password.html`. Development continued in Step 182 before a complete result could be claimed.

#### Next

[Step 180: Update Login Page](#step-180) · [Step 182: Update Forgot Password Page](#step-182)


---

<a id="step-182"></a>

### Step 182: Update Forgot Password Page

#### Before this step

The forgot-password screen was another page that needed the shared header and consistent spacing. Immediately before this, Step 181 dealt with update change password page; the work here continued the same part of the application.

#### What we did and why

We changed its HTML to reuse the common layout. The purpose of the page remains the same: ask for the email address associated with an account so Django can prepare a reset link.

#### Code and commands

**Step 182, block 1.** This code sample or terminal command was used while working on update forgot password page. The content below remains in its original sequence for Step 182, block 1.

```html
{% extends 'base.html' %}

{% block title %}Forgot Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-6 col-lg-5">

        <h2 class="mb-3">Forgot Password</h2>

        <p>
            Enter your registered email address
            to reset your password.
        </p>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-3">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Send Reset Link
            </button>
        </form>

        <p class="mt-3">
            <a href="{% url 'login' %}">
                Back to Login
            </a>
        </p>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 38 lines).** In Step 182, update forgot password page involved `base.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Forgot Password - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-6 col-lg-5">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-3">Forgot Password</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. The HTML element `<div class="mb-3">` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-primary">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** The HTML element `<p class="mt-3">` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}">` builds the address from a Django route name, so the link follows the route if its address later changes. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 36–38:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 182. For Step 182, the proposed way to check update forgot password page was to open the related page in the browser and check that the route reached the intended view or template in `base.html`. Development continued in Step 183 before a complete result could be claimed.

#### Next

[Step 181: Update Change Password Page](#step-181) · [Step 183: Update Reset Confirmation](#step-183)


---

<a id="step-183"></a>

### Step 183: Update Reset Confirmation

#### Before this step

The “check your email” screen appeared after a password-reset request, and it also needed the new layout. Immediately before this, Step 182 dealt with update forgot password page; the work here continued the same part of the application.

#### What we did and why

We kept the neutral confirmation message but placed it inside the shared page frame. The page is meant to reassure the requester without revealing whether that email address exists.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Password Reset Requested - Inventory System{% endbloc`; Replace lines around `{% block content %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-7 col-lg-6">`; Replace lines around `<h2 class="mb-4">Check Your Email</h2>`; Replace lines around `<div class="alert alert-info"> / If an active account exists with that email address,`. For Step 183, this comparison used the code block from Step 126, which might not represent the whole file.

**Step 183, block 1.** This code sample or terminal command was used while working on update reset confirmation. The content below remains in its original sequence for Step 183, block 1.

```html
{% extends 'base.html' %}

{% block title %}Password Reset Requested - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-6">

        <h2 class="mb-4">Check Your Email</h2>

        <div class="alert alert-info">
            If an active account exists with that email address,
            password reset instructions will be sent.
        </div>

        <p>
            Please check your email for the reset link.
        </p>

        <a href="{% url 'login' %}"
           class="btn btn-outline-primary mt-2">
            Back to Login
        </a>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 29 lines).** In Step 183, update reset confirmation involved `password_reset_done.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Password Reset Requested - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-7 col-lg-6">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-4">Check Your Email</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<div class="alert alert-info">` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}"` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 22–28:** `class="btn btn-outline-primary mt-2">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 29–29:** The lines starting with `{% endblock %}` supply the values or page content for this part of password_reset_done.html. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 183. For Step 183, the proposed way to check update reset confirmation was to revisit `password_reset_done.html` and run the corresponding command or feature to see its result. Development continued in Step 184 before a complete result could be claimed.

#### Next

[Step 182: Update Forgot Password Page](#step-182) · [Step 184: Update Set New Password Page](#step-184)


---

<a id="step-184"></a>

### Step 184: Update Set New Password Page

#### Before this step

The password-reset link opens a page outside the normal app navigation, so the recipient should immediately understand what to do. Immediately before this, Step 183 dealt with update reset confirmation; the work here continued the same part of the application.

#### What we did and why

The reset-confirm template uses the same layout and clearly shows the new-password form or an invalid-link message. Django still decides whether the token can be used; HTML styling does not make the link valid.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Set New Password - Inventory System{% endblock %}`; Replace lines around `{% block content %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-7 col-lg-5">`; Replace lines around `{% if validlink %}`; Replace lines around `<h2 class="mb-4">Set New Password</h2>`. For Step 184, this comparison used the code block from Step 126, which might not represent the whole file.

**Step 184, block 1.** This code sample or terminal command was used while working on update set new password page. The content below remains in its original sequence for Step 184, block 1.

```html
{% extends 'base.html' %}

{% block title %}Set New Password - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-5">

        {% if validlink %}

            <h2 class="mb-4">Set New Password</h2>

            <form method="POST">
                {% csrf_token %}

                <div class="mb-3">
                    {{ form.as_p }}
                </div>

                <button type="submit" class="btn btn-primary">
                    Reset Password
                </button>
            </form>

        {% else %}

            <h2 class="mb-4">Invalid Reset Link</h2>

            <div class="alert alert-warning">
                This password reset link is invalid or has
                expired. Please request a new one.
            </div>

            <a href="{% url 'password_reset' %}"
               class="btn btn-outline-primary">
                Request New Link
            </a>

        {% endif %}

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 45 lines).** In Step 184, update set new password page involved `password_reset_confirm.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Set New Password - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-7 col-lg-5">` creates or closes part of the page structure seen by the browser. `{% if validlink %}` decides which HTML is included based on the value Django provided. The HTML element `<h2 class="mb-4">Set New Password</h2>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page.

**Lines 15–21:** The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. The HTML element `<div class="mb-3">` creates or closes part of the page structure seen by the browser. `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-4">Invalid Reset Link</h2>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** The HTML element `<div class="alert alert-warning">` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'password_reset' %}"` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 36–42:** `class="btn btn-outline-primary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 43–45:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 184. For Step 184, the proposed way to check update set new password page was to open the related page in the browser and check that the route reached the intended view or template in `password_reset_confirm.html`. Development continued in Step 185 before a complete result could be claimed.

#### Next

[Step 183: Update Reset Confirmation](#step-183) · [Step 185: Update Reset Complete Page](#step-185)


---

<a id="step-185"></a>

### Step 185: Update Reset Complete Page

#### Before this step

After someone has successfully reset a password, the last screen should make the next action obvious. Immediately before this, Step 184 dealt with update set new password page; the work here continued the same part of the application.

#### What we did and why

We updated the completion page to match the rest of the site and guide the person back to login. No password details need to appear there.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Password Reset Complete - Inventory System{% endblock`; Replace lines around `{% block content %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-7 col-lg-5">`; Replace lines around `<h2 class="mb-4">Password Reset Successful</h2>`; Replace lines around `<div class="alert alert-success"> / Your password has been reset successfully.`. For Step 185, this comparison used the code block from Step 126, which might not represent the whole file.

**Step 185, block 1.** This code sample or terminal command was used while working on update reset complete page. The content below remains in its original sequence for Step 185, block 1.

```html
{% extends 'base.html' %}

{% block title %}Password Reset Complete - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-7 col-lg-5">

        <h2 class="mb-4">Password Reset Successful</h2>

        <div class="alert alert-success">
            Your password has been reset successfully.
            You can now log in using your new password.
        </div>

        <a href="{% url 'login' %}"
           class="btn btn-primary">
            Go to Login
        </a>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 25 lines).** In Step 185, update reset complete page involved `password_reset_complete.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Password Reset Complete - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-7 col-lg-5">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-4">Password Reset Successful</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<div class="alert alert-success">` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-primary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser.

**Lines 22–25:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 185. For Step 185, the proposed way to check update reset complete page was to open the related page in the browser and check that the route reached the intended view or template in `password_reset_complete.html`. Development continued in Step 186 before a complete result could be claimed.

#### Next

[Step 184: Update Set New Password Page](#step-184) · [Step 186: Update Employee List](#step-186)


---

<a id="step-186"></a>

### Step 186: Update Employee List

#### Before this step

The employee list had become the administrator's main account-management screen, so its table and buttons needed a clearer layout. Immediately before this, Step 185 dealt with update reset complete page; the work here continued the same part of the application.

#### What we did and why

We reused the shared template while keeping the employee rows and management actions together. The goal was to make it easy to find a person and understand which action each button performs.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %} / {% load static %}`; Replace lines around `{% block title %}Manage Employees - Inventory System{% endblock %}`; Replace lines around `{% block extra_css %} / <link rel="stylesheet" href="{% static 'accounts/style.css' %}">`; Replace lines around `{% block content %}`. For Step 186, this comparison used the code block from Step 169, which might not represent the whole file.

**Step 186, block 1.** This code sample or terminal command was used while working on update employee list. The content below remains in its original sequence for Step 186, block 1.

```html
{% extends 'base.html' %}
{% load static %}

{% block title %}Manage Employees - Inventory System{% endblock %}

{% block extra_css %}
<link rel="stylesheet" href="{% static 'accounts/style.css' %}">
{% endblock %}

{% block content %}

<h2 class="mb-4">Manage Employees</h2>

<div class="d-flex gap-2 mb-4">

    <a href="{% url 'create_employee' %}"
       class="btn btn-primary">
        Add New Employee
    </a>

    {% if edit_mode %}

        <a href="{% url 'employee_list' %}"
           class="btn btn-outline-secondary">
            Cancel Edit
        </a>

    {% else %}

        <a href="{% url 'employee_list' %}?mode=edit"
           class="btn btn-outline-primary">
            Edit Employee
        </a>

    {% endif %}

</div>

{% if edit_mode %}
    <p class="text-muted">
        Select an employee to edit.
    </p>
{% endif %}

<div class="table-responsive">

    <table class="table table-bordered table-hover align-middle">

        <thead class="table-light">
            <tr>
                <th>Username</th>
                <th>Name</th>
                <th>Email</th>
                <th>Role</th>
                <th>Status</th>
            </tr>
        </thead>

        <tbody>

            {% for employee in employees %}

            <tr {% if edit_mode %}class="selectable"{% endif %}>

                <td>
                    {% if edit_mode %}
                        <a class="row-link"
                           href="{% url 'edit_employee' employee.id %}">
                            {{ employee.username }}
                        </a>
                    {% else %}
                        {{ employee.username }}
                    {% endif %}
                </td>

                <td>
                    {{ employee.first_name }}
                    {{ employee.last_name }}
                </td>

                <td>{{ employee.email }}</td>

                <td>{{ employee.get_role_display }}</td>

                <td>
                    {% if employee.is_active %}
                        <span class="badge bg-success">Active</span>
                    {% else %}
                        <span class="badge bg-secondary">Inactive</span>
                    {% endif %}
                </td>

            </tr>

            {% empty %}

            <tr>
                <td colspan="5" class="text-center">
                    No employees found.
                </td>
            </tr>

            {% endfor %}

        </tbody>

    </table>

</div>

<a href="{% url 'home' %}"
   class="btn btn-outline-secondary mt-3">
    Back to Home
</a>

{% endblock %}
```

#### How the code works

**Block 1 (html, 116 lines).** In Step 186, update employee list involved `employee_list.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Manage Employees - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block extra_css %}` marks the part of the page where a child template supplies its own content. The link `<link rel="stylesheet" href="{% static 'accounts/style.css' %}">` lets the user navigate to the named screen from this page.

**Lines 8–14:** `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<h2 class="mb-4">Manage Employees</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<div class="d-flex gap-2 mb-4">` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** `<a href="{% url 'create_employee' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-primary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser. `{% if edit_mode %}` decides which HTML is included based on the value Django provided.

**Lines 22–28:** `<a href="{% url 'employee_list' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-secondary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** `<a href="{% url 'employee_list' %}?mode=edit"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-primary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser.

**Lines 36–42:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. `{% if edit_mode %}` decides which HTML is included based on the value Django provided. The HTML element `<p class="text-muted">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 43–49:** The HTML element `<div class="table-responsive">` creates or closes part of the page structure seen by the browser. The HTML element `<table class="table table-bordered table-hover align-middle">` creates or closes part of the page structure seen by the browser. The HTML element `<thead class="table-light">` creates or closes part of the page structure seen by the browser.

**Lines 50–56:** The HTML element `<tr>` creates or closes part of the page structure seen by the browser. The HTML element `<th>Username</th>` creates or closes part of the page structure seen by the browser. The HTML element `<th>Name</th>` creates or closes part of the page structure seen by the browser. The HTML element `<th>Email</th>` creates or closes part of the page structure seen by the browser.

**Lines 57–63:** The HTML element `</thead>` creates or closes part of the page structure seen by the browser. The HTML element `<tbody>` creates or closes part of the page structure seen by the browser. `{% for employee in employees %}` repeats the following operation once for each matching item. `<tr {% if edit_mode %}class="selectable"{% endif %}>` decides which HTML is included based on the value Django provided.

**Lines 64–70:** The HTML element `<td>` creates or closes part of the page structure seen by the browser. `{% if edit_mode %}` decides which HTML is included based on the value Django provided. The HTML element `<a class="row-link"` creates or closes part of the page structure seen by the browser. `href="{% url 'edit_employee' employee.id %}">` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 71–77:** The HTML element `</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>` creates or closes part of the page structure seen by the browser.

**Lines 78–84:** The HTML element `</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>{{ employee.email }}</td>` creates or closes part of the page structure seen by the browser. The HTML element `<td>{{ employee.get_role_display }}</td>` creates or closes part of the page structure seen by the browser.

**Lines 85–91:** The HTML element `<td>` creates or closes part of the page structure seen by the browser. `{% if employee.is_active %}` decides which HTML is included based on the value Django provided. The HTML element `<span class="badge bg-success">Active</span>` creates or closes part of the page structure seen by the browser. The HTML element `<span class="badge bg-secondary">Inactive</span>` creates or closes part of the page structure seen by the browser.

**Lines 92–98:** The HTML element `</tr>` creates or closes part of the page structure seen by the browser. The HTML element `<tr>` creates or closes part of the page structure seen by the browser. The HTML element `<td colspan="5" class="text-center">` creates or closes part of the page structure seen by the browser.

**Lines 99–105:** The HTML element `</td>` creates or closes part of the page structure seen by the browser. The HTML element `</tr>` creates or closes part of the page structure seen by the browser. The HTML element `</tbody>` creates or closes part of the page structure seen by the browser.

**Lines 106–112:** The HTML element `</table>` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'home' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-secondary mt-3">` sets the named value used by the next operation in this file.

**Lines 113–116:** The HTML element `</a>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 186. For Step 186, the proposed way to check update employee list was to revisit `employee_list.html` and run the corresponding command or feature to see its result. Development continued in Step 187 before a complete result could be claimed.

#### Next

[Step 185: Update Reset Complete Page](#step-185) · [Step 187: Update Create Employee](#step-187)


---

<a id="step-187"></a>

### Step 187: Update Create Employee

#### Before this step

The form for creating a new employee needed to look consistent with the newly styled employee list. Immediately before this, Step 186 dealt with update employee list; the work here continued the same part of the application.

#### What we did and why

We moved it into the common page layout and kept a clear form and save action. The server still validates the information before creating a user.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Create Employee - Inventory System{% endblock %}`; Replace lines around `{% block content %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-8 col-lg-6">`; Replace lines around `<h2 class="mb-3">Create Employee</h2>`; Replace lines around `<p class="text-muted mb-4"> / Enter the employee details below.`. For Step 187, this comparison used the code block from Step 141, which might not represent the whole file.

**Step 187, block 1.** This code sample or terminal command was used while working on update create employee. The content below remains in its original sequence for Step 187, block 1.

```html
{% extends 'base.html' %}

{% block title %}Create Employee - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-8 col-lg-6">

        <h2 class="mb-3">Create Employee</h2>

        <p class="text-muted mb-4">
            Enter the employee details below.
            A password setup link will be sent to their email.
        </p>

        <form method="POST">
            {% csrf_token %}

            <div class="mb-4">
                {{ form.as_p }}
            </div>

            <div class="d-flex gap-2">
                <button type="submit" class="btn btn-primary">
                    Create Employee
                </button>

                <a href="{% url 'employee_list' %}"
                   class="btn btn-outline-secondary">
                    Cancel
                </a>
            </div>
        </form>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 39 lines).** In Step 187, update create employee involved `create_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Create Employee - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-8 col-lg-6">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-3">Create Employee</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p class="text-muted mb-4">` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The HTML element `</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. The HTML element `<div class="mb-4">` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<div class="d-flex gap-2">` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-primary">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** `<a href="{% url 'employee_list' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-secondary">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser. The HTML element `</div>` creates or closes part of the page structure seen by the browser.

**Lines 36–39:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 187. For Step 187, the proposed way to check update create employee was to revisit `create_employee.html` and run the corresponding command or feature to see its result. Development continued in Step 188 before a complete result could be claimed.

#### Next

[Step 186: Update Employee List](#step-186) · [Step 188: Fix Form Styling](#step-188)


---

<a id="step-188"></a>

### Step 188: Fix Form Styling

#### Before this step

The pages looked more consistent, but the boxes and dropdowns generated by Django forms still looked plain. Immediately before this, Step 187 dealt with update create employee; the work here continued the same part of the application.

#### What we did and why

Django creates HTML form controls from its Python form fields. We needed to add styling instructions to those controls, not only to the page around them.

#### Code and commands

**Step 188, block 1.** This code sample or terminal command was used while working on fix form styling. The content below remains in its original sequence for Step 188, block 1.

```python
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

#### How the code works

**Block 1 (python, 5 lines).** In Step 188, fix form styling involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–5:** `for name, field in self.fields.items():` repeats the following operation once for each matching item. `if name == 'role':` is a condition: the code inside this branch only runs when its condition is satisfied. `field.widget.attrs['class'] = 'form-select'` sets the named value used by the next operation in this file. The `else` branch handles the alternative case when the preceding condition was not satisfied.

#### How we checked it

Not tested yet at Step 188. For Step 188, the proposed way to check fix form styling was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 189 before a complete result could be claimed.

#### Next

[Step 187: Update Create Employee](#step-187) · [Step 189: Add CSS classes to creation form widgets](#step-189)


---

<a id="step-189"></a>

### Step 189: Add CSS classes to creation form widgets

#### Before this step

The employee creation form had text fields and a role dropdown. We wanted both to use the correct styles without changing what they mean. Immediately before this, Step 188 dealt with fix form styling; the work here continued the same part of the application.

#### What we did and why

When Django builds the form, a small method adds a CSS class to each field. Text inputs get one class and the role dropdown another. This changes appearance, not which roles are allowed or how values are saved.

#### Code and commands

**Step 189, block 1.** This code sample or terminal command was used while working on add css classes to creation form widgets. The content below remains in its original sequence for Step 189, block 1.

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        # Bootstrap styling
        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

#### How the code works

**Block 1 (python, 14 lines).** In Step 189, add css classes to creation form widgets involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

**Lines 8–14:** The comment `# Bootstrap styling` explains the intention of the following section; it does not execute as application logic. `for name, field in self.fields.items():` repeats the following operation once for each matching item. `if name == 'role':` is a condition: the code inside this branch only runs when its condition is satisfied. `field.widget.attrs['class'] = 'form-select'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 189. For Step 189, the proposed way to check add css classes to creation form widgets was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 190 before a complete result could be claimed.

#### Next

[Step 188: Fix Form Styling](#step-188) · [Step 190: Correct Form Styling](#step-190)


---

<a id="step-190"></a>

### Step 190: Correct Form Styling

#### Before this step

We noticed the styling instructions were added to the wrong form in an earlier edit. Immediately before this, Step 189 dealt with add css classes to creation form widgets; the work here continued the same part of the application.

#### What we did and why

The project had a creation form and an edit form, and they are different Python classes. We corrected which class received the styling method, so the intended creation page would actually use it.

#### Code and commands

**Step 190, block 1.** This code sample or terminal command was used while working on correct form styling. The content below remains in its original sequence for Step 190, block 1.

```python
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

#### How the code works

**Block 1 (python, 13 lines).** In Step 190, correct form styling involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

**Lines 8–13:** `for name, field in self.fields.items():` repeats the following operation once for each matching item. `if name == 'role':` is a condition: the code inside this branch only runs when its condition is satisfied. `field.widget.attrs['class'] = 'form-select'` sets the named value used by the next operation in this file. The `else` branch handles the alternative case when the preceding condition was not satisfied.

#### How we checked it

Not tested yet at Step 190. For Step 190, the proposed way to check correct form styling was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 191 before a complete result could be claimed.

#### Next

[Step 189: Add CSS classes to creation form widgets](#step-189) · [Step 191: Consolidate both employee forms](#step-191)


---

<a id="step-191"></a>

### Step 191: Consolidate both employee forms

#### Before this step

After several small corrections, the complete form file was easier to understand than a patchwork of partial edits. Immediately before this, Step 190 dealt with correct form styling; the work here continued the same part of the application.

#### What we did and why

We brought the creation and edit form definitions together in one final version. This makes it clearer which fields create a new account, which fields edit an existing one, and where the styling is applied.

#### Code and commands

**Compared with the earlier version:** Insert lines around `from django import forms / from .models import CustomUser`; Insert lines around `def save(self, commit=True):`. For Step 191, this comparison used the code block from Step 190, which might not represent the whole file.

**Step 191, block 1.** This code sample or terminal command was used while working on consolidate both employee forms. The content below remains in its original sequence for Step 191, block 1.

```python
from django import forms
from .models import CustomUser


class EmployeeCreationForm(forms.ModelForm):

    class Meta:
        model = CustomUser
        fields = ['username', 'first_name', 'last_name', 'email', 'role']

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'

    def save(self, commit=True):
        user = super().save(commit=False)
        user.set_unusable_password()

        if commit:
            user.save()

        return user


class EmployeeEditForm(forms.ModelForm):

    class Meta:
        model = CustomUser
        fields = [
            'first_name',
            'last_name',
            'email',
            'role'
        ]

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        self.fields['role'].choices = [
            ('manager', 'Inventory Manager'),
            ('sales', 'Sales Staff')
        ]

        for name, field in self.fields.items():
            if name == 'role':
                field.widget.attrs['class'] = 'form-select'
            else:
                field.widget.attrs['class'] = 'form-control'
```

#### How the code works

**Block 1 (python, 58 lines).** In Step 191, consolidate both employee forms involved `accounts/forms.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django import forms` makes a Python function, class or module available to the code that follows. `from .models import CustomUser` makes a Python function, class or module available to the code that follows. `class EmployeeCreationForm(forms.ModelForm):` declares a Python class, grouping related data or behaviour together. `class Meta:` declares a Python class, grouping related data or behaviour together.

**Lines 8–14:** `model = CustomUser` sets the named value used by the next operation in this file. `fields = ['username', 'first_name', 'last_name', 'email', 'role']` sets the named value used by the next operation in this file. `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

**Lines 15–21:** `for name, field in self.fields.items():` repeats the following operation once for each matching item. `if name == 'role':` is a condition: the code inside this branch only runs when its condition is satisfied. `field.widget.attrs['class'] = 'form-select'` sets the named value used by the next operation in this file.

**Lines 22–28:** The `else` branch handles the alternative case when the preceding condition was not satisfied. `field.widget.attrs['class'] = 'form-control'` sets the named value used by the next operation in this file. `def save(self, commit=True):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `user = super().save(commit=False)` sets the named value used by the next operation in this file.

**Lines 29–35:** `if commit:` is a condition: the code inside this branch only runs when its condition is satisfied. `return user` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. `class EmployeeEditForm(forms.ModelForm):` declares a Python class, grouping related data or behaviour together.

**Lines 36–42:** `class Meta:` declares a Python class, grouping related data or behaviour together. `model = CustomUser` sets the named value used by the next operation in this file. `fields = [` sets the named value used by the next operation in this file.

**Lines 43–49:** `def __init__(self, *args, **kwargs):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called. `self.fields['role'].choices = [` sets the named value used by the next operation in this file.

**Lines 50–56:** `for name, field in self.fields.items():` repeats the following operation once for each matching item. `if name == 'role':` is a condition: the code inside this branch only runs when its condition is satisfied. `field.widget.attrs['class'] = 'form-select'` sets the named value used by the next operation in this file.

**Lines 57–58:** The `else` branch handles the alternative case when the preceding condition was not satisfied. `field.widget.attrs['class'] = 'form-control'` sets the named value used by the next operation in this file.

#### How we checked it

Not tested yet at Step 191. For Step 191, the proposed way to check consolidate both employee forms was to revisit `accounts/forms.py` and run the corresponding command or feature to see its result. Development continued in Step 192 before a complete result could be claimed.

#### Next

[Step 190: Correct Form Styling](#step-190) · [Step 192: Update Edit Employee](#step-192)


---

<a id="step-192"></a>

### Step 192: Update Edit Employee

#### Before this step

The edit-employee page was one of the remaining screens using its older layout. Immediately before this, Step 191 dealt with consolidate both employee forms; the work here continued the same part of the application.

#### What we did and why

We updated its HTML to reuse the common page frame, while keeping the employee's current values, save button and any status or resend actions. The actual update still happens in the view.

#### Code and commands

**Compared with the earlier version:** Replace lines around `{% extends 'base.html' %}`; Replace lines around `{% block title %}Edit Employee - Inventory System{% endblock %}`; Replace lines around `{% block content %}`; Replace lines around `<div class="row justify-content-center"> / <div class="col-md-8 col-lg-6">`; Replace lines around `<h2 class="mb-3">Edit Employee</h2>`; Replace lines around `<p class="text-muted"> / Username: <strong>{{ employee.username }}</strong>`. For Step 192, this comparison used the code block from Step 166, which might not represent the whole file.

**Step 192, block 1.** This code sample or terminal command was used while working on update edit employee. The content below remains in its original sequence for Step 192, block 1.

```html
{% extends 'base.html' %}

{% block title %}Edit Employee - Inventory System{% endblock %}

{% block content %}

<div class="row justify-content-center">
    <div class="col-md-8 col-lg-6">

        <h2 class="mb-3">Edit Employee</h2>

        <p class="text-muted">
            Username: <strong>{{ employee.username }}</strong>
        </p>

        <!-- EDIT EMPLOYEE DETAILS -->
        <form method="POST" class="mb-4">
            {% csrf_token %}

            <div class="mb-4">
                {{ form.as_p }}
            </div>

            <button type="submit" class="btn btn-primary">
                Save Changes
            </button>
        </form>

        <hr>

        <!-- EMPLOYEE STATUS -->
        <h5 class="mt-4">Account Status</h5>

        <p>
            Current status:
            {% if employee.is_active %}
                <span class="badge bg-success">Active</span>
            {% else %}
                <span class="badge bg-secondary">Inactive</span>
            {% endif %}
        </p>

        <form method="POST"
              action="{% url 'toggle_employee_status' employee.id %}"
              class="mb-4">

            {% csrf_token %}

            {% if employee.is_active %}
                <button type="submit" class="btn btn-outline-danger">
                    Deactivate Employee
                </button>
            {% else %}
                <button type="submit" class="btn btn-outline-success">
                    Reactivate Employee
                </button>
            {% endif %}

        </form>

        <!-- PASSWORD SETUP -->
        {% if employee.is_active and not employee.has_usable_password %}

            <hr>

            <h5 class="mt-4">Password Setup</h5>

            <p class="text-muted">
                This employee has not set a password yet.
            </p>

            <form method="POST"
                  action="{% url 'resend_setup_email' employee.id %}"
                  class="mb-4">

                {% csrf_token %}

                <button type="submit" class="btn btn-outline-primary">
                    Resend Setup Email
                </button>

            </form>

        {% endif %}

        <hr>

        <a href="{% url 'employee_list' %}"
           class="btn btn-outline-secondary mt-2">
            Back to Manage Employees
        </a>

    </div>
</div>

{% endblock %}
```

#### How the code works

**Block 1 (html, 96 lines).** In Step 192, update edit employee involved `edit_employee.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `{% extends 'base.html' %}` tells this page to reuse a shared layout rather than repeating the entire page structure. `{% block title %}Edit Employee - Inventory System{% endblock %}` marks the part of the page where a child template supplies its own content. `{% block content %}` marks the part of the page where a child template supplies its own content. The HTML element `<div class="row justify-content-center">` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<div class="col-md-8 col-lg-6">` creates or closes part of the page structure seen by the browser. The HTML element `<h2 class="mb-3">Edit Employee</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p class="text-muted">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 15–21:** The comment `<!-- EDIT EMPLOYEE DETAILS -->` explains the intention of the following section; it does not execute as application logic. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted. The HTML element `<div class="mb-4">` creates or closes part of the page structure seen by the browser.

**Lines 22–28:** The HTML element `</div>` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-primary">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Lines 29–35:** The HTML element `<hr>` creates or closes part of the page structure seen by the browser. The comment `<!-- EMPLOYEE STATUS -->` explains the intention of the following section; it does not execute as application logic. The HTML element `<h5 class="mt-4">Account Status</h5>` creates or closes part of the page structure seen by the browser. The HTML element `<p>` creates or closes part of the page structure seen by the browser.

**Lines 36–42:** `{% if employee.is_active %}` decides which HTML is included based on the value Django provided. The HTML element `<span class="badge bg-success">Active</span>` creates or closes part of the page structure seen by the browser. The HTML element `<span class="badge bg-secondary">Inactive</span>` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 43–49:** The form sends submitted fields to the server as a POST request, rather than just opening a page. `action="{% url 'toggle_employee_status' employee.id %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="mb-4">` sets the named value used by the next operation in this file. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 50–56:** The HTML element `<button type="submit" class="btn btn-outline-danger">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `<button type="submit" class="btn btn-outline-success">` creates or closes part of the page structure seen by the browser.

**Lines 57–63:** The HTML element `</form>` creates or closes part of the page structure seen by the browser. The comment `<!-- PASSWORD SETUP -->` explains the intention of the following section; it does not execute as application logic. `{% if employee.is_active and not employee.has_usable_password %}` decides which HTML is included based on the value Django provided.

**Lines 64–70:** The HTML element `<hr>` creates or closes part of the page structure seen by the browser. The HTML element `<h5 class="mt-4">Password Setup</h5>` creates or closes part of the page structure seen by the browser. The HTML element `<p class="text-muted">` creates or closes part of the page structure seen by the browser. The HTML element `</p>` creates or closes part of the page structure seen by the browser.

**Lines 71–77:** The form sends submitted fields to the server as a POST request, rather than just opening a page. `action="{% url 'resend_setup_email' employee.id %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="mb-4">` sets the named value used by the next operation in this file. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 78–84:** The HTML element `<button type="submit" class="btn btn-outline-primary">` creates or closes part of the page structure seen by the browser. The HTML element `</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser.

**Lines 85–91:** The HTML element `<hr>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'employee_list' %}"` builds the address from a Django route name, so the link follows the route if its address later changes. `class="btn btn-outline-secondary mt-2">` sets the named value used by the next operation in this file. The HTML element `</a>` creates or closes part of the page structure seen by the browser.

**Lines 92–96:** The HTML element `</div>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 192. For Step 192, the proposed way to check update edit employee was to revisit `edit_employee.html` and run the corresponding command or feature to see its result. Development continued in Step 193 before a complete result could be claimed.

#### Next

[Step 191: Consolidate both employee forms](#step-191) · [Step 193: Email Error Handling](#step-193)
