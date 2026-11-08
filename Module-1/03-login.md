# MODULE 1 — Accounts

## Chapter 3: Login (Steps 73–79)

**Where we are.** An account table alone cannot sign a user in. We connected a browser form to a Django authentication view and a URL.

**What this chapter gives us.** Trace username/password POST data through authenticate(), login(), redirect(), and a session.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain what happens when a user submits valid versus invalid credentials.

### Find a step

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

**Why we are doing this.** We wanted staff to sign in from the inventory application, not rely on the built-in admin login.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Open accounts/views.py and replace its contents with.*

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

**How it works.** The login view receives form data on POST, calls `authenticate` to check credentials, calls `login` to start a session, and redirects successful users. A GET request simply displays the login form.

**Follow the flow.** A normal page visit is a GET request, so `loginpage` renders `accounts/login.html`. Submitting the form sends a POST with the username and password. `authenticate` checks them and returns a user object only when they are valid. `login` starts the session; `redirect('/')` asks the browser to open the homepage. With wrong details, `messages.error` prepares feedback and the form is shown again. The password is never compared to a stored plain-text password by our view.

**How we checked it.** The view cannot be opened from a browser until its template and URL are connected in Steps 75–77.

**Connects to.** Previous: [Step 72 — Register CustomUser](02-database-setup-and-admin-site.md#step-72) · Next: [Step 74 — Understand models.py versus admin.py](03-login.md#step-74)


---

<a id="step-74"></a>

### Step 74: Understand models.py versus admin.py

**Why we are doing this.** The files `models.py` and `admin.py` were easy to confuse, so we clarified their responsibilities before proceeding.

**File / location.** `accounts/models.py and accounts/admin.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; In our project (illustrative or partial; not a complete paste-ready file).*

```python
# models.py
class CustomUser(AbstractUser):
    role = models.CharField(...)
```

**How it works.** `models.py` defines the stored data and model methods; `admin.py` configures Django Admin's management screens for those models. Editing one does not replace the other.

**How we checked it.** The examples are illustrative; there was no additional feature in this step.

**Connects to.** Previous: [Step 73 — Login View](03-login.md#step-73) · Next: [Step 75 — Login Page](03-login.md#step-75)


---

<a id="step-75"></a>

### Step 75: Login Page

**Why we are doing this.** A browser needs an HTML form so a person can submit the username and password to the view built in Step 73.

**File / location.** `templates/accounts/login.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add.*

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

**How it works.** The template renders input fields and submits a POST request. Django's CSRF token prevents certain forged form submissions.

**Follow the flow.** The form is the browser-side half of the workflow. Its inputs provide names the Python view reads from `request.POST`. `method="POST"` sends submitted values as a form submission, and the CSRF token helps Django check that submission. The HTML does not authenticate the user itself.

**How we checked it.** The file exists here, but the route is connected in Steps 76–77; successful login is tested after that.

**Connects to.** Previous: [Step 74 — Understand models.py versus admin.py](03-login.md#step-74) · Next: [Step 76 — Login URL](03-login.md#step-76)


---

<a id="step-76"></a>

### Step 76: Login URL

**Why we are doing this.** Although the view and HTML file existed, Django still needed a URL within the accounts app that pointed to the view.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Paste.*

```python
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.loginpage, name='login'),
]
```

**How it works.** `path('login/', views.loginpage, name='login')` maps a request path to the Python function. The route name allows templates to refer to it without hardcoding the address.

**How we checked it.** The app's URL module must still be included in the project's top-level URL configuration.

**Connects to.** Previous: [Step 75 — Login Page](03-login.md#step-75) · Next: [Step 77 — Connect Login URL](03-login.md#step-77)


---

<a id="step-77"></a>

### Step 77: Connect Login URL

**Why we are doing this.** Django starts URL resolution from the project-level `config/urls.py`, so the new app URLs had to be included there.

**File / location.** `config/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Replace its contents with.*

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("accounts.urls")),
    path("", include("core.urls")),
]
```

*2. Recorded snippet; We added.*

```python
path("", include("accounts.urls")),
```

**How it works.** `include('accounts.urls')` delegates matching account-related paths to the accounts app. This completes the browser-to-URL-to-view-to-template chain.

**Follow the flow.** The outer URL file is like a receptionist: it sees an incoming path and delegates it to the right app. Including `accounts.urls` allows `/login/` to reach `loginpage`. Including `core.urls` allows the homepage to reach the core view. The project and app URL files therefore work together.

**How we checked it.** The original notes then discuss redirect behavior, showing the route connections were being reviewed.

**Connects to.** Previous: [Step 76 — Login URL](03-login.md#step-76) · Next: [Step 78 — Login Redirect](03-login.md#step-78)


---

<a id="step-78"></a>

### Step 78: Login Redirect

**Why we are doing this.** After successful login, the view sends users to `/`. We wanted to understand why that route opens the application homepage.

**File / location.** `accounts/views.py and core/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Because of this line in accounts/views.py.*

```python
return redirect('/')
```

**How it works.** `redirect('/')` sends a new browser request to the root URL, where the core app's URL/view renders home. Logging in and rendering the homepage are separate requests.

**How we checked it.** This is an explanation of existing code, not a new redirect implementation.

**Connects to.** Previous: [Step 77 — Connect Login URL](03-login.md#step-77) · Next: [Step 79 — Understand a Django project versus an app](03-login.md#step-79)


---

<a id="step-79"></a>

### Step 79: Understand a Django project versus an app

**Why we are doing this.** Django's project and app folder names can look similar; misunderstanding them makes imports and file paths confusing.

**File / location.** `Django project folder structure`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; For example, when you run.*

```bash
django-admin startproject myproject
```

**How it works.** A Django project holds global settings and URL routing; apps hold features such as `accounts` and `core`. `manage.py` is the command launcher at project level.

**How we checked it.** The sample project tree illustrates the concept; no new `startproject` was intended here.

**Connects to.** Previous: [Step 78 — Login Redirect](03-login.md#step-78) · Next: [Step 80 — Logout Function](04-logout.md#step-80)

---

**Chapter recap.** Trace username/password POST data through authenticate(), login(), redirect(), and a session.

**Practice explaining:** Explain what happens when a user submits valid versus invalid credentials.

