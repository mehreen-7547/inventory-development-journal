# MODULE 2 — Supplier and inventory foundations

## Chapter 2: Create Supplier (Steps 248–251)

### Before we begin

Chapter 1 created the `catalog` app, Supplier database model and a Manager-only Supplier List page. It did **not** yet provide a way to add suppliers. The existing `SupplierForm` already included name, phone, email and address, so we could reuse it without adding another model or table.

### What we worked on

We connected a create view, an HTML form, a named URL and a navigation link. We kept the feature deliberately small: Managers create suppliers, the form validates inputs, and successful submissions return to the supplier list. Editing and deactivation belong to later chapters.

### Picture it this way

The list is the supplier address book. The Add Supplier link opens a blank entry card; the form collects details; the view checks them and stores the record; then the browser returns to the address book.

> **How to use these notes:** These are development-history notes, not commands to rerun on a working system. Steps 387–390 were the interactive session labels; the continuous **journal** numbers here are 248–251. The user confirmed the steps locally. This chapter does not claim the source repository was already pushed or that automated tests ran.

### Jump to a step

- [Step 248: Create the Manager-only Supplier Create view](#step-248)
- [Step 249: Create the Add Supplier HTML form](#step-249)
- [Step 250: Register the Add Supplier URL and test saving](#step-250)
- [Step 251: Add the Add Supplier link to the list page](#step-251)

---

<a id="step-248"></a>

### Step 248: Create the Manager-only Supplier Create view

**What was happening before this step?**

The supplier list existed, but Managers could not add records through the website.

**What did we do, and what does it mean?**

On GET, the view creates an empty SupplierForm. On POST, it binds submitted fields, validates them and saves a new Supplier only if valid. The redirect prevents the saved form from being submitted again by refreshing the result page. Both decorators restrict access to authenticated Managers. Keep the existing supplier_list view; add only the new import and function.

**File or place:** `catalog/views.py`.

**Code or command from this step**

*Add the imports if missing and add the new function below the existing supplier_list view.*

```python
from django.shortcuts import redirect
from .forms import SupplierForm

@login_required
@role_required('manager')
def supplier_create(request):
    if request.method == 'POST':
        form = SupplierForm(request.POST)

        if form.is_valid():
            form.save()
            return redirect('supplier_list')
    else:
        form = SupplierForm()

    return render(request, 'catalog/supplier_form.html', {
        'form': form
    })
```

**Understanding the code, one piece at a time**

`request.method` tells us whether the browser is opening the page (GET) or submitting it (POST). `form.is_valid()` checks input rules before `form.save()` writes to the database. `redirect` sends the browser to a new URL after saving.

**How did we know what happened?**

The user confirmed Step 387 was completed locally. This view depends on the template and URL added in the next two steps.

**Where to go next:** Previous: [Step 247 — Verify supplier list](01-supplier-setup-and-list.md#step-247) · Next: [Step 249](#step-249)

---

<a id="step-249"></a>

### Step 249: Create the Add Supplier HTML form

**What was happening before this step?**

The view knew how to save a Supplier, but the browser had no page for entering data.

**What did we do, and what does it mean?**

Template inheritance reuses the existing base.html layout. The CSRF token protects the POST request. form.as_p displays the four fields declared in SupplierForm: name, phone, email and address. is_active is intentionally excluded. The link returns to the existing supplier list.

**File or place:** `catalog/templates/catalog/supplier_form.html`.

**Code or command from this step**

*Use this content in the named file.*

```html
{% extends 'base.html' %}

{% block content %}

<h2>Add Supplier</h2>

<form method="POST">
    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit">Save Supplier</button>
</form>

<p>
    <a href="{% url 'supplier_list' %}">Back to Suppliers</a>
</p>

{% endblock %}
```

**Understanding the code, one piece at a time**

`{% extends %}` reuses the page layout; `{% csrf_token %}` is required for normal Django POST forms; `{{ form.as_p }}` renders the ModelForm fields; the button submits them.

**How did we know what happened?**

The user confirmed Step 388 was completed locally. This was a new template file, not a replacement for supplier_list.html.

**Where to go next:** Previous: [Step 248](#step-248) · Next: [Step 250](#step-250)

---

<a id="step-250"></a>

### Step 250: Register the Add Supplier URL and test saving

**What was happening before this step?**

The view and template existed, but no browser route pointed to supplier_create.

**What did we do, and what does it mean?**

The suppliers/add/ path calls supplier_create, while the existing suppliers/ path continues to show the list. The route name supplier_create can be used in Django templates without hardcoding the address. After submission, the view redirects to the named supplier_list route.

**File or place:** `catalog/urls.py`.

**Code or command from this step**

*Use this content in the named file.*

```python
from django.urls import path
from . import views

urlpatterns = [
    path('suppliers/', views.supplier_list, name='supplier_list'),
    path('suppliers/add/', views.supplier_create, name='supplier_create'),
]
```

**Understanding the code, one piece at a time**

The path text is the browser address; `views.supplier_create` is the Python function that handles it; `name='supplier_create'` is a stable label used by templates.

**How did we know what happened?**

The user continued after being asked to test the form at http://127.0.0.1:8000/suppliers/add/ and was given fictional sample supplier details: Kerala Wholesale Traders, 9876543210, supplier@example.com, MG Road, Ernakulam, Kerala. The conversation does not include a separate screenshot of the saved record; avoid claiming an independent database verification.

**Where to go next:** Previous: [Step 249](#step-249) · Next: [Step 251](#step-251)

---

<a id="step-251"></a>

### Step 251: Add the Add Supplier link to the list page

**What was happening before this step?**

Managers could open the form by typing its URL, but the list page had no navigation link.

**What did we do, and what does it mean?**

The Django url template tag resolves supplier_create to the route registered in Step 250. This link is placed immediately after the Suppliers heading, before the existing table. The table and empty-state message remain unchanged.

**File or place:** `catalog/templates/catalog/supplier_list.html`.

**Code or command from this step**

*Insert the paragraph immediately below the existing heading; keep the table.*

```html
<h2>Suppliers</h2>

<p>
    <a href="{% url 'supplier_create' %}">Add Supplier</a>
</p>
```

**Understanding the code, one piece at a time**

The link is ordinary HTML, but `{% url 'supplier_create' %}` is resolved by Django. If the path changes later, the named link can continue working.

**How did we know what happened?**

The user explicitly confirmed that the Add Supplier link worked and marked Chapter 2 complete.

**Where to go next:** Previous: [Step 250](#step-250) · Next: Chapter 3 — Edit Supplier (not yet documented)

---

### The point of this chapter

The Create Supplier workflow is implemented locally and the user confirmed its navigation link works. A ModelForm keeps field definitions in one place, a function-based view handles GET/POST, a template displays the form, and a named route connects the pieces. This chapter introduces no additional tables, roles or external dependencies.

**Interview practice:** Explain the difference between GET and POST, why `is_valid()` runs before `save()`, what a CSRF token does, why we redirect after saving, and how a named URL connects the list page to the create form.
