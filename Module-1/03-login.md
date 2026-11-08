# Module 1, Chapter 3: Login

## Step 73: Login View

#### Next: Create the login function

Open `accounts/views.py` and replace its contents with:

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

**What it does:** Checks the username and password, logs in the user if valid, and redirects to the homepage.

This follows the login-function approach taught in class.

**Expected result:** The file saves without errors. We'll create the login page next.

*Previous: Step 72 (Register CustomUser)  |  Next: Step 74 (models.py vs admin.py)*

---

## Step 74: models.py vs admin.py

Because the two files have **different purposes**.

- **`models.py`** defines what information is stored in the database.
- **`admin.py`** defines how that information is displayed and managed in Django Admin.

In our project:

```python
# models.py
class CustomUser(AbstractUser):
    role = models.CharField(...)
```

This creates the `role` database field.

```python
# admin.py
class CustomUserAdmin(UserAdmin):
    ...
```

This makes the `role` field available in Django Admin's user forms.

**Simply put:** `models.py` defines the data; `admin.py` controls its administration interface.

*Previous: Step 73 (Login View)  |  Next: Step 75 (Login Page)*

---

## Step 75: Login Page

#### Next: Create the login page

Create a file at **`templates/accounts/login.html`**.

Add:

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

**What this does:** Creates a form where users enter their username and password. The form sends these values to the `loginpage()` function we created earlier.

**Expected result:** The file saves successfully. We will connect the page to a URL next.

*Previous: Step 74 (models.py vs admin.py)  |  Next: Step 76 (Login URL)*

---

## Step 76: Login URL

#### Next: Create `accounts/urls.py`

Inside the existing `accounts` folder, create a file named `urls.py`.

Paste:

```python
from django.urls import path
from . import views

urlpatterns = [
    path('login/', views.loginpage, name='login'),
]
```

**What this does:** Connects the URL `/login/` to our `loginpage()` function in `accounts/views.py`.

**Expected result:** File saves successfully. Next, we'll connect these URLs to the main project.

*Previous: Step 75 (Login Page)  |  Next: Step 77 (Connect Login URL)*

---
