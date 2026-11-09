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
