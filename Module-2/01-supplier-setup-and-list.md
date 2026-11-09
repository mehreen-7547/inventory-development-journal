# MODULE 2 — Supplier and inventory foundations

## Chapter 1: Supplier setup and list (Steps 236–247)

### Before we begin

Module 1 employee accounts was complete. We needed a new Django app to store supplier and product information, then a manager-only page for viewing suppliers. We also needed to respect the approved v3 architecture while retaining the supplier address field requested during development.

### What we worked on

We created and registered `catalog`, defined and refined the Supplier model, prepared its database table, built a ModelForm, view, HTML list and URL, and resolved a missing-table error. Supplier creation, editing and deactivation are **not** implemented in this chapter.

### Picture it this way

The Supplier table is an address book for businesses that provide products. The model describes each entry; the migration creates storage; the view reads entries; the template displays them; and the URL opens the page. A manager can view the list even when it is empty.

> **Study note:** This records the local development session as reported and demonstrated by the user. Module 2 source files were not yet pushed to the main code repository when this chapter was written. Do not replay the historical failed migration sequence on a working database.

### Jump to a step

- [Step 236: Create the catalog Django app](#step-236)
- [Step 237: Register catalog in Django settings](#step-237)
- [Step 238: Define the first Supplier model](#step-238)
- [Step 239: Preview the Supplier migration](#step-239)
- [Step 240: Review the supplier fields against v3](#step-240)
- [Step 241: Finalize the five-field Supplier model](#step-241)
- [Step 242: Generate and apply the Supplier migration](#step-242)
- [Step 243: Create the Supplier ModelForm](#step-243)
- [Step 244: Build a Manager-only Supplier List view](#step-244)
- [Step 245: Create the Supplier List template](#step-245)
- [Step 246: Connect the Supplier URL](#step-246)
- [Step 247: Diagnose the missing SQLite table and verify the page](#step-247)

---

<a id="step-236"></a>

### Step 236: Create the catalog Django app

**What was happening before this step?**

We needed a separate home for suppliers and products, rather than mixing them with employee accounts.

**What did we do, and what does it mean?**

Django generated the catalog app directory, including models.py, views.py, apps.py and migrations/. The user confirmed creation.

**File or place:** `Project root — terminal`.

**Code or command from this step**

```powershell
python manage.py startapp catalog
```

**Understanding the code, one piece at a time**

These commands run from the directory containing manage.py. A migration preview is not the same as an applied migration; the [X] marker in showmigrations indicates applied state.

**How did we know what happened?**

Django generated the catalog app directory, including models.py, views.py, apps.py and migrations/. The user confirmed creation.

**Where to go next:** Previous: [Step 235 — Module 1 complete](../Module-1/24-testing-complete-password-reset-and-first-time-employee-setup.md#step-235) · Next: [Step 237](#step-237)

---

<a id="step-237"></a>

### Step 237: Register catalog in Django settings

**What was happening before this step?**

Creating a folder alone does not tell Django to load its models.

**What did we do, and what does it mean?**

This is an illustrative excerpt, not a replacement settings list. The user added catalog to the existing INSTALLED_APPS and confirmed python manage.py check passed.

**File or place:** `config/settings.py`.

**Code or command from this step**

```python
INSTALLED_APPS = [
    # Keep the existing Django, core and accounts entries.
    'catalog',
]
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

This is an illustrative excerpt, not a replacement settings list. The user added catalog to the existing INSTALLED_APPS and confirmed python manage.py check passed.

**Where to go next:** Previous: [Step 236](#step-236) · Next: [Step 238](#step-238)

---

<a id="step-238"></a>

### Step 238: Define the first Supplier model

**What was happening before this step?**

Before building pages, we needed to decide which information a supplier record stores.

**What did we do, and what does it mean?**

This was the initial draft, not the final model. The contact_person field was removed in a later decision.

**File or place:** `catalog/models.py`.

**Code or command from this step**

```python
from django.db import models


class Supplier(models.Model):
    name = models.CharField(max_length=150)
    contact_person = models.CharField(max_length=100, blank=True)
    phone = models.CharField(max_length=20, blank=True)
    email = models.EmailField(blank=True)
    address = models.TextField(blank=True)
    is_active = models.BooleanField(default=True)

    def __str__(self):
        return self.name
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

This was the initial draft, not the final model. The contact_person field was removed in a later decision.

**Where to go next:** Previous: [Step 237](#step-237) · Next: [Step 239](#step-239)

---

<a id="step-239"></a>

### Step 239: Preview the Supplier migration

**What was happening before this step?**

We wanted to check what table Django planned to create before changing the database.

**What did we do, and what does it mean?**

The preview showed Create model Supplier. A dry run does not save a migration file or create a database table.

**File or place:** `Project root — terminal`.

**Code or command from this step**

```powershell
python manage.py check
python manage.py makemigrations catalog --dry-run
```

**Understanding the code, one piece at a time**

These commands run from the directory containing manage.py. A migration preview is not the same as an applied migration; the [X] marker in showmigrations indicates applied state.

**How did we know what happened?**

The preview showed Create model Supplier. A dry run does not save a migration file or create a database table.

**Where to go next:** Previous: [Step 238](#step-238) · Next: [Step 240](#step-240)

---

<a id="step-240"></a>

### Step 240: Review the supplier fields against v3

**What was happening before this step?**

The approved v3 baseline originally listed name, phone, email and is_active. We caught an unnecessary contact_person field.

**What did we do, and what does it mean?**

The user explicitly chose to retain address because it is useful for supplier and delivery information, but remove contact_person. This is a user-approved small extension of v3.

**File or place:** `catalog/models.py`.

**Code or command from this step**

```python
# Final field choices are shown in Step 240.
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

The user explicitly chose to retain address because it is useful for supplier and delivery information, but remove contact_person. This is a user-approved small extension of v3.

**Where to go next:** Previous: [Step 239](#step-239) · Next: [Step 241](#step-241)

---

<a id="step-241"></a>

### Step 241: Finalize the five-field Supplier model

**What was happening before this step?**

We needed one unambiguous schema before generating a migration.

**What did we do, and what does it mean?**

name is required; contact fields and address are optional; new suppliers default to active. No separate Category table is added.

**File or place:** `catalog/models.py`.

**Code or command from this step**

```python
from django.db import models


class Supplier(models.Model):
    name = models.CharField(max_length=150)
    phone = models.CharField(max_length=20, blank=True)
    email = models.EmailField(blank=True)
    address = models.TextField(blank=True)
    is_active = models.BooleanField(default=True)

    def __str__(self):
        return self.name
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

name is required; contact fields and address are optional; new suppliers default to active. No separate Category table is added.

**Where to go next:** Previous: [Step 240](#step-240) · Next: [Step 242](#step-242)

---

<a id="step-242"></a>

### Step 242: Generate and apply the Supplier migration

**What was happening before this step?**

A Django model is Python code; a migration creates its corresponding SQLite table.

**What did we do, and what does it mean?**

The user initially reported a successful migration, but a later runtime error revealed the current project had no catalog migration. We investigated and repaired this in Step 245. Do not assume the first report established the table existed.

**File or place:** `Project root — terminal`.

**Code or command from this step**

```powershell
python manage.py makemigrations catalog
python manage.py migrate catalog
python manage.py showmigrations catalog
```

**Understanding the code, one piece at a time**

These commands run from the directory containing manage.py. A migration preview is not the same as an applied migration; the [X] marker in showmigrations indicates applied state.

**How did we know what happened?**

The user initially reported a successful migration, but a later runtime error revealed the current project had no catalog migration. We investigated and repaired this in Step 245. Do not assume the first report established the table existed.

**Where to go next:** Previous: [Step 241](#step-241) · Next: [Step 243](#step-243)

---

<a id="step-243"></a>

### Step 243: Create the Supplier ModelForm

**What was happening before this step?**

Managers need inputs for adding and editing supplier details without exposing activation status as an ordinary form field.

**What did we do, and what does it mean?**

ModelForm builds form inputs from the model. is_active is intentionally omitted; status changes will use a separate workflow.

**File or place:** `catalog/forms.py`.

**Code or command from this step**

```python
from django import forms
from .models import Supplier


class SupplierForm(forms.ModelForm):
    class Meta:
        model = Supplier
        fields = ['name', 'phone', 'email', 'address']
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

ModelForm builds form inputs from the model. is_active is intentionally omitted; status changes will use a separate workflow.

**Where to go next:** Previous: [Step 242](#step-242) · Next: [Step 244](#step-244)

---

<a id="step-244"></a>

### Step 244: Build a Manager-only Supplier List view

**What was happening before this step?**

We needed a controlled database query and a way to pass its results to HTML.

**What did we do, and what does it mean?**

Both active and inactive suppliers are listed alphabetically. The existing role_required decorator restricts this management view to Manager, not Admin or Sales.

**File or place:** `catalog/views.py`.

**Code or command from this step**

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required
from accounts.decorators import role_required
from .models import Supplier


@login_required
@role_required('manager')
def supplier_list(request):
    suppliers = Supplier.objects.all().order_by('name')
    return render(request, 'catalog/supplier_list.html', {
        'suppliers': suppliers
    })
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

Both active and inactive suppliers are listed alphabetically. The existing role_required decorator restricts this management view to Manager, not Admin or Sales.

**Where to go next:** Previous: [Step 243](#step-243) · Next: [Step 245](#step-245)

---

<a id="step-245"></a>

### Step 245: Create the Supplier List template

**What was happening before this step?**

The view retrieved data, but the browser needed an HTML table to show it.

**What did we do, and what does it mean?**

The template uses the existing base layout and shows an explicit empty state. Compact Folders in VS Code displayed templates/catalog on one line, which was normal.

**File or place:** `catalog/templates/catalog/supplier_list.html`.

**Code or command from this step**

```html
{% extends 'base.html' %}
{% block content %}
<h2>Suppliers</h2>
<table border="1">
    <tr><th>Name</th><th>Phone</th><th>Email</th><th>Address</th><th>Status</th></tr>
    {% for supplier in suppliers %}
    <tr>
        <td>{{ supplier.name }}</td>
        <td>{{ supplier.phone }}</td>
        <td>{{ supplier.email }}</td>
        <td>{{ supplier.address }}</td>
        <td>{% if supplier.is_active %}Active{% else %}Inactive{% endif %}</td>
    </tr>
    {% empty %}
    <tr><td colspan="5">No suppliers found.</td></tr>
    {% endfor %}
</table>
{% endblock %}
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

The template uses the existing base layout and shows an explicit empty state. Compact Folders in VS Code displayed templates/catalog on one line, which was normal.

**Where to go next:** Previous: [Step 244](#step-244) · Next: [Step 246](#step-246)

---

<a id="step-246"></a>

### Step 246: Connect the Supplier URL

**What was happening before this step?**

Without routing, the browser cannot reach the supplier_list function.

**What did we do, and what does it mean?**

In config/urls.py we also added path('', include('catalog.urls')) to the existing urlpatterns, preserving the core and accounts routes.

**File or place:** `catalog/urls.py`.

**Code or command from this step**

```python
from django.urls import path
from . import views

urlpatterns = [
    path('suppliers/', views.supplier_list, name='supplier_list'),
]
```

**Understanding the code, one piece at a time**

This code belongs in the file named above. The model defines data, the form controls inputs, the view handles requests, and the template displays data. Each has a different responsibility.

**How did we know what happened?**

In config/urls.py we also added path('', include('catalog.urls')) to the existing urlpatterns, preserving the core and accounts routes.

**Where to go next:** Previous: [Step 245](#step-245) · Next: [Step 247](#step-247)

---

<a id="step-247"></a>

### Step 247: Diagnose the missing SQLite table and verify the page

**What was happening before this step?**

Opening /suppliers/ raised OperationalError: no such table: catalog_supplier, despite earlier migration checks.

**What did we do, and what does it mean?**

The user supplied the [ ] migration result and later a screenshot of /suppliers/ rendering the Suppliers table and 'No suppliers found.' The user also stated Manager-only access was already tested. No automated Module 2 test-suite result or GitHub source push was provided.

**File or place:** `Project root — terminal`.

**Code or command from this step**

```powershell
python manage.py showmigrations catalog
# Initially: catalog (no migrations)
python manage.py makemigrations catalog
# Then: [ ] 0001_initial
python manage.py migrate catalog
python manage.py showmigrations catalog
# Expected: [X] 0001_initial
```

**Understanding the code, one piece at a time**

These commands run from the directory containing manage.py. A migration preview is not the same as an applied migration; the [X] marker in showmigrations indicates applied state.

**How did we know what happened?**

The user supplied the [ ] migration result and later a screenshot of /suppliers/ rendering the Suppliers table and 'No suppliers found.' The user also stated Manager-only access was already tested. No automated Module 2 test-suite result or GitHub source push was provided.

**Where to go next:** Previous: [Step 246](#step-246) · Next: Chapter 2 — Create Supplier (not yet documented)

---

### The point of this chapter

A database-backed, Manager-only Supplier List is available locally, including an empty state. We learned that `makemigrations --dry-run` does not create migration files, and that a missing SQLite table should be investigated before resetting anything.

**Interview practice:** Explain how `catalog/models.py`, `catalog/forms.py`, `catalog/views.py`, `catalog/urls.py` and `catalog/templates/catalog/supplier_list.html` connect. Why does `is_active` stay out of the ordinary form? Why does an empty list still mean the page works?
