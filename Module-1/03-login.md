# MODULE 1 — Employee accounts

## Chapter 3: Login (Steps 73–79)

### Before we begin

The administrator could use Django's built-in site, but ordinary employees needed their own login screen inside the inventory website.

### What we worked on

We built a login function, an HTML form and the URL rules that connect them. We also traced what happens when login sends the browser to the homepage.

### Picture it this way

A typical visit goes: browser opens login page, employee types credentials, Django checks them, then the browser is sent to the home address. The next chapter adds a way to log out.

### Find a step

- [Step 73: Login View](#step-73)
- [Step 74: Understand models.py versus admin.py](#step-74)
- [Step 75: Login Page](#step-75)
- [Step 76: Login URL](#step-76)
- [Step 77: Connect Login URL](#step-77)

---

<a id="step-73"></a>

### Step 73: Login View

#### Before this step

Until now, the only built-in account screen was Django Admin. Employees needed a normal login page inside the inventory application. The preceding step, Step 72, covered register customuser, so this was the next part of the development sequence.

#### What we did and why

A view receives the browser's request, reads the submitted username and password, and asks Django to check them. If they are correct, Django signs the person in and redirects; if not, the form can explain that login failed.

#### Code and commands

**Step 73, block 1.** This code sample or terminal command was used while working on login view. The content below remains in its original sequence for Step 73, block 1.

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

#### How the code works

**Block 1 (python, 23 lines).** In Step 73, login view involved `accounts/views.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.shortcuts import render, redirect` makes a Python function, class or module available to the code that follows. `from django.contrib.auth import authenticate, login` makes a Python function, class or module available to the code that follows. `from django.contrib import messages` makes a Python function, class or module available to the code that follows. `def loginpage(request):` begins the function responsible for the operation described in this step; the indented lines beneath it run when that function is called.

**Lines 8–14:** `username = request.POST.get('username')` sets the named value used by the next operation in this file. `password = request.POST.get('password')` sets the named value used by the next operation in this file. `user = authenticate(` sets the named value used by the next operation in this file. `username=username,` sets the named value used by the next operation in this file.

**Lines 15–21:** `if user is not None:` is a condition: the code inside this branch only runs when its condition is satisfied. `return redirect('/')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser. `messages.error(request, 'Invalid username or password.')` prepares feedback that can appear when the user reaches the next page.

**Lines 22–23:** `return render(request, 'accounts/login.html')` sends the result back to the caller; for a Django view, this usually determines the response shown in the browser.

#### How we checked it

Not tested yet at Step 73. For Step 73, the proposed way to check login view was to open the related page in the browser and check that the route reached the intended view or template in `accounts/views.py`. Development continued in Step 74 before a complete result could be claimed.

#### Next

[Step 72: Register CustomUser](../Module-1/02-database-setup-and-admin-site.md#step-72) · [Step 74: Understand models.py versus admin.py](#step-74)


---

<a id="step-74"></a>

### Step 74: Understand models.py versus admin.py

#### Before this step

The filenames models.py and admin.py looked similar, but they do different jobs. Immediately before this, Step 73 dealt with login view; the work here continued the same part of the application.

#### What we did and why

models.py defines what information exists in the database. admin.py decides how that information appears inside Django's administrative interface. Neither file replaces the login view.

#### Code and commands

**Step 74, block 1.** This code sample or terminal command was used while working on understand models.py versus admin.py. The content below remains in its original sequence for Step 74, block 1.

```python
# models.py
class CustomUser(AbstractUser):
    role = models.CharField(...)
```

**Step 74, block 2.** This code sample or terminal command was used while working on understand models.py versus admin.py. The content below remains in its original sequence for Step 74, block 2.

```python
# admin.py
class CustomUserAdmin(UserAdmin):
    ...
```

#### How the code works

**Block 1 (python, 3 lines).** In Step 74, understand models.py versus admin.py involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** The comment `# models.py` explains the intention of the following section; it does not execute as application logic. `class CustomUser(AbstractUser):` declares a Python class, grouping related data or behaviour together. `role = models.CharField(...)` sets the named value used by the next operation in this file.

**Block 2 (python, 3 lines).** In Step 74, understand models.py versus admin.py involved `models.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–3:** The comment `# admin.py` explains the intention of the following section; it does not execute as application logic. `class CustomUserAdmin(UserAdmin):` declares a Python class, grouping related data or behaviour together.

#### How we checked it

Not tested yet at Step 74. For Step 74, the proposed way to check understand models.py versus admin.py was to run the appropriate Django migration or model check and inspect its result in the terminal. Development continued in Step 75 before a complete result could be claimed.

#### Next

[Step 73: Login View](#step-73) · [Step 75: Login Page](#step-75)


---

<a id="step-75"></a>

### Step 75: Login Page

#### Before this step

The login view was written, but an employee still needed a page where they could type a username and password. Immediately before this, Step 74 dealt with understand models.py versus admin.py; the work here continued the same part of the application.

#### What we did and why

The HTML template creates the form shown in the browser. It sends the typed information to the login view. The password field hides characters on screen, while Django handles checking the actual credentials on the server.

#### Code and commands

**Step 75, block 1.** This code sample or terminal command was used while working on login page. The content below remains in its original sequence for Step 75, block 1.

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

#### How the code works

**Block 1 (html, 25 lines).** In Step 75, login page involved `templates/accounts/login.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Login</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** `{% for message in messages %}` repeats the following operation once for each matching item. The HTML element `<p>{{ message }}</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** The HTML element `<input type="text" name="username"` creates or closes part of the page structure seen by the browser. `placeholder="Username" required>` sets the named value used by the next operation in this file. The HTML element `<input type="password" name="password"` creates or closes part of the page structure seen by the browser. `placeholder="Password" required>` sets the named value used by the next operation in this file.

**Lines 22–25:** The HTML element `<button type="submit">Login</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser. The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 75. For Step 75, the proposed way to check login page was to open the related page in the browser and check that the route reached the intended view or template in `templates/accounts/login.html`. Development continued in Step 76 before a complete result could be claimed.

#### Next

[Step 74: Understand models.py versus admin.py](#step-74) · [Step 76: Login URL](#step-76)


---

<a id="step-76"></a>

### Step 76: Login URL

#### Before this step

We had a login function and a login page, but no web address linked to them yet. Immediately before this, Step 75 dealt with login page; the work here continued the same part of the application.

#### What we did and why

A URL rule maps an address such as /accounts/login/ to a view. Adding it in the accounts app is like putting a signpost in front of an existing room.

#### Code and commands

**Step 76, block 1.** This code sample or terminal command was used while working on login url. The content below remains in its original sequence for Step 76, block 1.

```python
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.loginpage, name='login'),
]
```

#### How the code works

**Block 1 (python, 6 lines).** In Step 76, login url involved `accounts/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–6:** `from django.urls import path` makes a Python function, class or module available to the code that follows. `from . import views` makes a Python function, class or module available to the code that follows. `urlpatterns = [` sets the named value used by the next operation in this file. `path('login/', views.loginpage, name='login'),` begins a URL rule connecting a browser address with the function that answers it.

#### How we checked it

Not tested yet at Step 76. For Step 76, the proposed way to check login url was to open the related page in the browser and check that the route reached the intended view or template in `accounts/urls.py`. Development continued in Step 77 before a complete result could be claimed.

#### Next

[Step 75: Login Page](#step-75) · [Step 77: Connect Login URL](#step-77)


---

<a id="step-77"></a>

### Step 77: Connect Login URL

#### Before this step

Django's project-level URL file is the first place it looks when a browser requests a page. Immediately before this, Step 76 dealt with login url; the work here continued the same part of the application.

#### What we did and why

Including the accounts URL file means /accounts/... addresses can reach the views inside our accounts app. Without that connection, even a correct login view may return “page not found.”.

#### Code and commands

**Step 77, block 1.** This code sample or terminal command was used while working on connect login url. The content below remains in its original sequence for Step 77, block 1.

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("accounts.urls")),
    path("", include("core.urls")),
]
```

**Step 77, block 2.** This code sample or terminal command was used while working on connect login url. The content below remains in its original sequence for Step 77, block 2.

```python
path("", include("accounts.urls")),
```

**Step 77, block 3.** This code sample or terminal command was used while working on connect login url. The content below remains in its original sequence for Step 77, block 3.

```powershell
Test-Path .\templates\accounts\login.html
```

**Step 77, block 4.** This output was used while working on connect login url. The content below remains in its original sequence for Step 77, block 4.

```text
techmindz_inventory_module0_sqlite_atomic/
├── manage.py
└── templates/
    └── accounts/
        └── login.html
```

#### How the code works

**Block 1 (python, 8 lines).** In Step 77, connect login url involved `config/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** `from django.contrib import admin` makes a Python function, class or module available to the code that follows. `from django.urls import include, path` makes a Python function, class or module available to the code that follows. `urlpatterns = [` sets the named value used by the next operation in this file. `path("admin/", admin.site.urls),` begins a URL rule connecting a browser address with the function that answers it.

**Lines 8–8:** The lines starting with `]` supply the values or page content for this part of config/urls.py. These lines are kept together because their order and indentation matter.

**Block 2 (python, 1 lines).** In Step 77, connect login url involved `config/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `path("", include("accounts.urls")),` begins a URL rule connecting a browser address with the function that answers it.

**Block 3 (powershell, 1 lines).** In Step 77, connect login url involved `config/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Test-Path .\templates\accounts\login.html` supply the values or page content for this part of config/urls.py. These lines are kept together because their order and indentation matter.

**Block 4 (text, 5 lines).** In Step 77, connect login url involved `config/urls.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 77, output block 4 showed the result rather than another command. Its visible value was `techmindz_inventory_module0_sqlite_atomic/`, which mattered for connect login url.

#### How we checked it

Not tested yet at Step 77. For Step 77, the proposed way to check connect login url was to open the related page in the browser and check that the route reached the intended view or template in `config/urls.py`. Development continued in Step 78 before a complete result could be claimed.

#### Next

[Step 76: Login URL](#step-76) · [Step 78: Login Redirect](#step-78)
