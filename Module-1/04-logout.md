# MODULE 1 — Accounts

## Chapter 4: Logout (Steps 80–82)

**Where we are.** Login begins a session; logout must safely end it and give users a visible control.

**What this chapter gives us.** Connect a POST-protected logout view, named URL, and CSRF-protected form.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why logout is a state-changing POST request.

### Find a step

- [Step 80: Logout Function](#step-80)
- [Step 81: Logout URL](#step-81)
- [Step 82: Logout Button](#step-82)

---

<a id="step-80"></a>

### Step 80: Logout Function

**Why we are doing this.** Successful login created sessions; users also needed a way to end them securely.

**File / location.** `accounts/views.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Change the existing authentication import to.*

```python
from django.contrib.auth import authenticate, login, logout
```

*2. Recorded snippet; Add this import.*

```python
from django.views.decorators.http import require_POST
```

*3. Recorded snippet; Add this function below loginpage().*

```python
@require_POST
def logoutpage(request):
    logout(request)
    return redirect('login')
```

**How it works.** The logout view calls Django's `logout(request)` to clear session authentication. Using a POST-only endpoint prevents ordinary GET links from triggering state-changing logout.

**Follow the flow.** `@require_POST` rejects ordinary GET requests to the logout endpoint. Inside the view, `logout(request)` clears the authenticated session. Only after that does the redirect send the browser back to the login page. The HTML form from Step 82 supplies the required POST request.

**How we checked it.** The URL and button were still missing, so this function was not yet a complete browser workflow.

**Connects to.** Previous: [Step 79 — Understand a Django project versus an app](03-login.md#step-79) · Next: [Step 81 — Logout URL](04-logout.md#step-81)


---

<a id="step-81"></a>

### Step 81: Logout URL

**Why we are doing this.** The logout function existed, but users needed an address Django could route to it.

**File / location.** `accounts/urls.py`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Add the logout path below the login path.*

```python
urlpatterns = [
    path('login/', views.loginpage, name='login'),
    path('logout/', views.logoutpage, name='logout'),
]
```

**How it works.** An app URL pattern maps `/logout/` to `logoutpage`. The named URL can be reused in the logout form.

**How we checked it.** The next step adds the actual form on the homepage.

**Connects to.** Previous: [Step 80 — Logout Function](04-logout.md#step-80) · Next: [Step 82 — Logout Button](04-logout.md#step-82)


---

<a id="step-82"></a>

### Step 82: Logout Button

**Why we are doing this.** Users needed a visible control that would submit the logout POST request.

**File / location.** `core/templates/core/home.html`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Inside the <body>, add.*

```html
<form method="POST" action="{% url 'logout' %}">
    {% csrf_token %}
    <button type="submit">Logout</button>
</form>
```

**How it works.** The HTML form uses `{% url 'logout' %}` to build the endpoint and `{% csrf_token %}` to protect the request. The submit button triggers the POST.

The token helps Django reject forged state-changing form submissions.

**How we checked it.** To verify, log in, use Logout, then try visiting a protected page again; the notes do not record a distinct test result here.

**Connects to.** Previous: [Step 81 — Logout URL](04-logout.md#step-81) · Next: [Step 83 — Protect Homepage](05-homepage-protection-and-navigation.md#step-83)

---

**Chapter recap.** Connect a POST-protected logout view, named URL, and CSRF-protected form.

**Practice explaining:** Explain why logout is a state-changing POST request.

