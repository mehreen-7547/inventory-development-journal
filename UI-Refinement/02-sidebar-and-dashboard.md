# UI refinement — Chapter 2: Sidebar and Manager dashboard (Step 542)

## Step 542 — Replace scattered navigation with a role-specific sidebar

**Goal:** Replace the dated top navigation and scattered dashboard buttons with a consistent application shell and a clearer Manager dashboard.

**File: MODIFIED** `templates/base.html`

**Source excerpt:**
```django
{% if user.role == 'admin' %}
    <a href="{% url 'employee_list' %}" class="app-nav-link {% if '/employees/' in request.path %}active{% endif %}">Employees</a>
{% elif user.role == 'manager' %}
    <a href="{% url 'product_list' %}" class="app-nav-link {% if '/products/' in request.path %}active{% endif %}">Products</a>
    <a href="{% url 'supplier_list' %}" class="app-nav-link {% if '/suppliers/' in request.path %}active{% endif %}">Suppliers</a>
{% endif %}
```

**Why:** Each user sees navigation relevant to their role. The existing URL names and permission checks remain in the Django views; hiding links is not a substitute for authorization. A single base template provides the shell on every page. The template uses one `{% block content %}` to keep Django inheritance valid.

**File: MODIFIED** `core/static/core/ui.css`

**Source excerpt:**
```css
.app-shell { display: flex; min-height: 100vh; }
.app-sidebar { width: 228px; flex: 0 0 228px; background: #fff; }
.app-workspace { flex: 1; min-width: 0; }
```

**Why:** Sidebar, header, content width, active links and mobile toggle are controlled centrally. Existing Bootstrap styles still handle forms and data tables.

**File: MODIFIED** `core/templates/core/home.html`

**What changed:** Replaced the verbose dashboard heading with “Overview”; moved six Manager summary metrics above the attention table; moved redundant navigation buttons into a collapsed Quick actions section. Kept the links and dashboard data, without changing Python business logic.

**Source commits:** `6852f2a`, `ca5c701`, `4db8a3a` (an intermediate base-template commit was superseded).

**Verification:** GitHub edits published. **Local Django template rendering, tests and visual review are pending.** Check all three roles, login/password reset, Manager dashboard, navigation, and mobile menu. Do not mark this step locally verified until those checks pass.

**Next:** Pull source `main`, run `python manage.py check` and browser verification. Correct any issues before changing additional pages.
