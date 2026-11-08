# MODULE 1 — Employee accounts

## Chapter 4: Logout (Steps 80–82)

### Before we begin

Users could log in, but there was no way to end the signed-in session from the website.

### What we worked on

We added the logout function, connected a URL to it and placed a logout control on the homepage.

### Picture it this way

Signing in creates a session so Django remembers who you are. Logging out ends that session. A link or button by itself does not end it; the request must reach the logout view.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 80: Logout Function](#step-80)
- [Step 81: Logout URL](#step-81)
- [Step 82: Logout Button](#step-82)

---

<a id="step-80"></a>

### Step 80: Logout Function

**What was happening before this step?**

We could sign in, but we also needed a safe way to sign out.

**What did we do, and what does it mean?**

Logout tells Django to end the current login session. The session is what lets the website remember the signed-in person across multiple pages. Ending it means the next protected request needs a login again.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Change the existing authentication import to.*

```python
from django.contrib.auth import authenticate, login, logout
```

*2. Add this import.*

```python
from django.views.decorators.http import require_POST
```

*3. Add this function below loginpage().*

```python
@require_POST
def logoutpage(request):
    logout(request)
    return redirect('login')
```

**Understanding the code, one piece at a time**

`logout(request)` tells Django to clear the current login session. It does not delete the user record or remove the person's username from the database.

**How did we know what happened?**

The URL and button were still missing, so this function was not yet a complete browser workflow.

**Where to go next:** Previous: [Step 79 — Understand a Django project versus an app](03-login.md#step-79) · Next: [Step 81 — Logout URL](04-logout.md#step-81)


---

<a id="step-81"></a>

### Step 81: Logout URL

**What was happening before this step?**

The logout function was ready, but the browser did not have an address for calling it.

**What did we do, and what does it mean?**

Adding the logout URL connects a named address to the function. The address alone is not a button; we build the visible control next.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Add the logout path below the login path.*

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
]
```

**Understanding the code, one piece at a time**

`path(...)` connects a browser address to a Django view. The `name` is a nickname other files can use when creating a link.

**How did we know what happened?**

The next step adds the actual form on the homepage.

**Where to go next:** Previous: [Step 80 — Logout Function](04-logout.md#step-80) · Next: [Step 82 — Logout Button](04-logout.md#step-82)


---

<a id="step-82"></a>

### Step 82: Logout Button

**What was happening before this step?**

A user should not have to type a logout URL manually.

**What did we do, and what does it mean?**

The homepage gained a form or button that sends a POST request to the logout view. POST is a browser request used for an action that changes state, such as ending a session, rather than simply reading a page.

**File or place:** `core/templates/core/home.html`.

**Code or command from this step**

*1. Inside the <body>, add.*

```html
<form method="POST" action="{% url 'logout' %}">
    {% csrf_token %}
    <button type="submit">Logout</button>
</form>
```

**Understanding the code, one piece at a time**

`{% csrf_token %}` adds a hidden safety value to the form, so Django can check that a request really came through the site. `{{ form.as_p }}` asks Django to display the fields as simple paragraphs.

**How did we know what happened?**

To verify, log in, use Logout, then try visiting a protected page again; the notes do not record a distinct test result here.

**Where to go next:** Previous: [Step 81 — Logout URL](04-logout.md#step-81) · Next: [Step 83 — Protect Homepage](05-homepage-protection-and-navigation.md#step-83)

---

### The point of this chapter

Signing in creates a session so Django remembers who you are. Logging out ends that session. A link or button by itself does not end it; the request must reach the logout view.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

