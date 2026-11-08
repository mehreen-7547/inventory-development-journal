# MODULE 1 — Employee accounts

## Chapter 3: Login (Steps 73–79)

### Before we begin

The administrator could use Django's built-in site, but ordinary employees needed their own login screen inside the inventory website.

### What we worked on

We built a login function, an HTML form and the URL rules that connect them. We also traced what happens when login sends the browser to the homepage.

### Picture it this way

A typical visit goes: browser opens login page, employee types credentials, Django checks them, then the browser is sent to the home address. The next chapter adds a way to log out.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 73: Login View](#step-73)
- [Step 74: Understand models.py versus admin.py](#step-74)
- [Step 75: Login Page](#step-75)
- [Step 76: Login URL](#step-76)
- [Step 77: Connect Login URL](#step-77)
- [Step 78: Login Redirect](#step-78)
- [Step 79: Understand a Django project versus an app](#step-79)

---

<a id="step-73"></a>

### Step 73: Login View

**What was happening before this step?**

Until now, the only built-in account screen was Django Admin. Employees needed a normal login page inside the inventory application.

**What did we do, and what does it mean?**

A view receives the browser's request, reads the submitted username and password, and asks Django to check them. If they are correct, Django signs the person in and redirects; if not, the form can explain that login failed.

**File or place:** `accounts/views.py`.

**Code or command from this step**

*1. Open accounts/views.py and replace its contents with.*

```python
from django.shortcuts import render, redirect
from django.contrib.auth import authenticate, login
from django.contrib import messages


def loginpage(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')

        user = authenticate(
            request,
            username=username,
            password=password
        )

        if user is not None:
            login(request, user)
            return redirect('/')

        messages.error(request, 'Invalid username or password.')

    return render(request, 'accounts/login.html')
```

**Understanding the code, one piece at a time**

`authenticate(...)` asks Django whether the submitted login details match an account. `login(request, user)` then starts a session for that accepted user. The two actions are separate: checking credentials is not the same as remembering the login.

**How did we know what happened?**

The view cannot be opened from a browser until its template and URL are connected in Steps 75–77.

**Where to go next:** Previous: [Step 72 — Register CustomUser](02-database-setup-and-admin-site.md#step-72) · Next: [Step 74 — Understand models.py versus admin.py](03-login.md#step-74)


---

<a id="step-74"></a>

### Step 74: Understand models.py versus admin.py

**What was happening before this step?**

The filenames `models.py` and `admin.py` looked similar, but they do different jobs.

**What did we do, and what does it mean?**

`models.py` defines what information exists in the database. `admin.py` decides how that information appears inside Django's administrative interface. Neither file replaces the login view.

**File or place:** `accounts/models.py and accounts/admin.py`.

**Code or command from this step**

*1. In our project. This is a shortened illustration, not a complete file.*

```python
# models.py
class CustomUser(AbstractUser):
    role = models.CharField(...)
```

**How did we know what happened?**

The examples are illustrative; there was no additional feature in this step.

**Where to go next:** Previous: [Step 73 — Login View](03-login.md#step-73) · Next: [Step 75 — Login Page](03-login.md#step-75)


---

<a id="step-75"></a>

### Step 75: Login Page

**What was happening before this step?**

The login view was written, but an employee still needed a page where they could type a username and password.

**What did we do, and what does it mean?**

The HTML template creates the form shown in the browser. It sends the typed information to the login view. The password field hides characters on screen, while Django handles checking the actual credentials on the server.

**File or place:** `templates/accounts/login.html`.

**Code or command from this step**

*1. Add.*

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
</head>
<body>
    <h2>Login</h2>

    {% for message in messages %}
        <p>{{ message }}</p>
    {% endfor %}

    <form method="POST">
        {% csrf_token %}

        <input type="text" name="username"
               placeholder="Username" required>

        <input type="password" name="password"
               placeholder="Password" required>

        <button type="submit">Login</button>
    </form>
</body>
</html>
```

**Understanding the code, one piece at a time**

The browser shows the fields declared in the HTML form. When submitted, the browser sends the entered data back to Django. The view reads it and decides whether the login succeeds. The HTML page should not make that decision itself.

**How did we know what happened?**

The file exists here, but the route is connected in Steps 76–77; successful login is tested after that.

**Where to go next:** Previous: [Step 74 — Understand models.py versus admin.py](03-login.md#step-74) · Next: [Step 76 — Login URL](03-login.md#step-76)


---

<a id="step-76"></a>

### Step 76: Login URL

**What was happening before this step?**

We had a login function and a login page, but no web address linked to them yet.

**What did we do, and what does it mean?**

A URL rule maps an address such as `/accounts/login/` to a view. Adding it in the accounts app is like putting a signpost in front of an existing room.

**File or place:** `accounts/urls.py`.

**Code or command from this step**

*1. Paste.*

```python
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.loginpage, name='login'),
]
```

**Understanding the code, one piece at a time**

The `name='login'` part of a URL rule gives the route a nickname. Other templates and views can refer to that nickname instead of typing the address in several places.

**How did we know what happened?**

The app's URL module must still be included in the project's top-level URL configuration.

**Where to go next:** Previous: [Step 75 — Login Page](03-login.md#step-75) · Next: [Step 77 — Connect Login URL](03-login.md#step-77)


---

<a id="step-77"></a>

### Step 77: Connect Login URL

**What was happening before this step?**

Django's project-level URL file is the first place it looks when a browser requests a page.

**What did we do, and what does it mean?**

Including the accounts URL file means `/accounts/...` addresses can reach the views inside our accounts app. Without that connection, even a correct login view may return “page not found.”

**File or place:** `config/urls.py`.

**Code or command from this step**

*1. Replace its contents with.*

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("accounts.urls")),
    path("", include("core.urls")),
]
```

*2. We added.*

```python
path("", include("accounts.urls")),
```

**Understanding the code, one piece at a time**

`include('accounts.urls')` means “for addresses that start with this prefix, look in the accounts app's URL list.” It keeps the project URL file shorter as more pages are added.

**How did we know what happened?**

The notes then discuss redirect behavior, showing the route connections were being reviewed.

**Where to go next:** Previous: [Step 76 — Login URL](03-login.md#step-76) · Next: [Step 78 — Login Redirect](03-login.md#step-78)


---

<a id="step-78"></a>

### Step 78: Login Redirect

**What was happening before this step?**

After a successful login, we sent the user to the home address `/`. We needed to know where that address leads.

**What did we do, and what does it mean?**

Django matches `/` against the project URL rules and hands the request to the homepage view. The login function does not have to create the homepage itself; it simply sends the browser there.

**File or place:** `accounts/views.py and core/urls.py`.

**Code or command from this step**

*1. Because of this line in accounts/views.py.*

```python
return redirect('/')
```

**Understanding the code, one piece at a time**

`redirect(...)` does not display a page itself. It tells the browser which page to open next, often after a form has been saved.

**How did we know what happened?**

This is an explanation of existing code, not a new redirect implementation.

**Where to go next:** Previous: [Step 77 — Connect Login URL](03-login.md#step-77) · Next: [Step 79 — Understand a Django project versus an app](03-login.md#step-79)


---

<a id="step-79"></a>

### Step 79: Understand a Django project versus an app

**What was happening before this step?**

The `config` folder and the `accounts` folder both contained Python files such as `urls.py`, which could be confusing.

**What did we do, and what does it mean?**

`config` holds settings and the main map of website addresses. `accounts` holds one collection of related features. The project connects several apps rather than putting everything in one file.

**File or place:** `Django project folder structure`.

**Code or command from this step**

*1. For example, when you run.*

```bash
django-admin startproject myproject
```

**How did we know what happened?**

The sample project tree illustrates the concept; no new `startproject` was intended here.

**Where to go next:** Previous: [Step 78 — Login Redirect](03-login.md#step-78) · Next: [Step 80 — Logout Function](04-logout.md#step-80)

---

### The point of this chapter

A typical visit goes: browser opens login page, employee types credentials, Django checks them, then the browser is sent to the home address. The next chapter adds a way to log out.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

