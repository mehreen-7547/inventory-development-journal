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
