# MODULE 2 — Supplier and inventory foundations

## Chapter 3: Edit Supplier (Steps 252–260)

### Before we begin
Chapter 2 had a Manager-only Supplier List and an Add Supplier form. The Supplier model and ModelForm already existed, so editing required no new database fields or migrations. The user wanted the same **single Edit button and selectable-row interaction** as Employee Management, with a separate external stylesheet.

### What we built
A Manager can open the Supplier List, click **Edit Supplier**, select an existing supplier, review its prefilled fields, update and save it, or cancel without saving. The same form template also serves Add Supplier. The list uses a dedicated external CSS file for its layout and table styling, alongside existing Bootstrap button and badge classes.

### Picture it this way
The Supplier List is an address book. **Edit Supplier** changes the page into selection mode; choosing a name opens that existing address card. **Update Supplier** changes the saved card. **Cancel** returns to the list without submitting it.

> **Numbering:** Interactive conversation steps **391–398**, including substeps 396A–396D and troubleshooting, are consolidated into continuous journal steps **252–260**. This is a study journal, not a command log to execute again.
>
> **Evidence:** Source repository `mehreen-7547/ai-assisted-inventory-manager`, branch `main`, commit [`2b580fb`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/2b580fbd82ed4285f972c07cb8daf35782ea932c). The user reported local UI and Django system-check success. We verified the five published source files on GitHub; we did not run the application ourselves.

### Jump to a step
- [Step 252: Create the Manager-only Supplier Edit view](#step-252)
- [Step 253: Register and verify the supplier-specific edit URL](#step-253)
- [Step 254: Reuse the Supplier form for create and edit](#step-254)
- [Step 255: Recover the Supplier List after a template mix-up](#step-255)
- [Step 256: Adopt the single Edit Supplier button and selection mode](#step-256)
- [Step 257: Make Supplier rows selectable only in edit mode](#step-257)
- [Step 258: Create an external Catalog stylesheet](#step-258)
- [Step 259: Connect the CSS and diagnose the initial 404](#step-259)
- [Step 260: Verify Chapter 3 and publish the code repository](#step-260)

---

<a id="step-252"></a>

### Step 252: Create the Manager-only Supplier Edit view

**What was happening before this step?**

The list and creation workflow existed, but there was no server action for changing an existing supplier.

**What did we do, and what does it mean?**

The view looks up one Supplier by its ID, shows its current values on GET, and binds submitted values to that same record on POST. `instance=supplier` is essential: without it, ModelForm.save() would create a different supplier instead of updating this one.

**File or place:** `catalog/views.py`.

**Code or command from this step**

**Filename: `catalog/views.py`**

```python
from django.shortcuts import get_object_or_404

@login_required
@role_required('manager')
def supplier_edit(request, supplier_id):
    supplier = get_object_or_404(Supplier, id=supplier_id)

    if request.method == 'POST':
        form = SupplierForm(request.POST, instance=supplier)
        if form.is_valid():
            form.save()
            return redirect('supplier_list')
    else:
        form = SupplierForm(instance=supplier)

    return render(request, 'catalog/supplier_form.html', {
        'form': form
    })
```

**Understanding the code, one piece at a time**

`get_object_or_404` returns a 404 when the ID does not exist; it does not silently create a record. The login and role decorators restrict the view to logged-in Managers. Invalid POST data re-renders the form with errors and does not save.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 251](02-create-supplier.md#step-251) · Next: [Step 253](#step-253)

---

<a id="step-253"></a>

### Step 253: Register and verify the supplier-specific edit URL

**What was happening before this step?**

Even a correct view cannot be opened from the browser until Django knows its route.

**What did we do, and what does it mean?**

A dynamic integer URL segment captures the supplier's primary key and passes it as `supplier_id` to the view.

**File or place:** `catalog/urls.py`.

**Code or command from this step**

**Filename: `catalog/urls.py`**

```python
path('suppliers/<int:supplier_id>/edit/', views.supplier_edit, name='supplier_edit'),
```

**Understanding the code, one piece at a time**

The user verified that `/suppliers/1/edit/` opened a form prefilled with an existing supplier. This demonstrates the URL, view, database lookup and GET form binding worked together; it does not by itself verify POST.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 252](#step-252) · Next: [Step 254](#step-254)

---

<a id="step-254"></a>

### Step 254: Reuse the Supplier form for create and edit

**What was happening before this step?**

The original form said Add Supplier and Save Supplier even when an existing record was being edited.

**What did we do, and what does it mean?**

The same template can distinguish a saved instance from a new unsaved instance by checking its primary key.

**File or place:** `catalog/templates/catalog/supplier_form.html`.

**Code or command from this step**

**Filename: `catalog/templates/catalog/supplier_form.html`**

```html
{% if form.instance.pk %}
    <h2>Edit Supplier</h2>
{% else %}
    <h2>Add Supplier</h2>
{% endif %}

<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    {% if form.instance.pk %}
        <button type="submit" class="btn btn-primary">Update Supplier</button>
        <a href="{% url 'supplier_list' %}" class="btn btn-outline-secondary">Cancel</a>
    {% else %}
        <button type="submit" class="btn btn-primary">Save Supplier</button>
    {% endif %}
</form>
```

**Understanding the code, one piece at a time**

The `form` object comes from either supplier_create or supplier_edit. `form.instance.pk` exists for an existing saved supplier. A Cancel link navigates without submitting a POST. The published template also has a Back to Suppliers link below the form.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 253](#step-253) · Next: [Step 255](#step-255)

---

<a id="step-255"></a>

### Step 255: Recover the Supplier List after a template mix-up

**What was happening before this step?**

During the work, the list URL temporarily displayed Add Supplier without fields, and Back to Suppliers returned to the same page.

**What did we do, and what does it mean?**

The shared form belongs in `supplier_form.html`, not `supplier_list.html`. The list view passes `suppliers`, whereas the form template expects `form`. We restored the list template before continuing.

**File or place:** `catalog/templates/catalog/supplier_list.html`.

**Code or command from this step**

**Filename: `catalog/templates/catalog/supplier_list.html`**

```html
{% for supplier in suppliers %}
    <tr>
        <td>{{ supplier.name }}</td>
        <td>{{ supplier.phone }}</td>
    </tr>
{% empty %}
    <tr><td colspan="5">No suppliers found.</td></tr>
{% endfor %}
```

**Understanding the code, one piece at a time**

This is a teaching excerpt from the restored list, not the complete final table. The user showed a working list with two active suppliers afterward. The difference between template context keys explains why the wrong template rendered without the expected fields.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 254](#step-254) · Next: [Step 256](#step-256)

---

<a id="step-256"></a>

### Step 256: Adopt the single Edit Supplier button and selection mode

**What was happening before this step?**

An Edit link in every row was functional, but the requested interface was the existing Employee Management pattern: one Edit button above the table.

**What did we do, and what does it mean?**

The list view derives `edit_mode` from the query string. The template toggles between Edit Supplier and Cancel Edit; in edit mode the supplier name becomes a link to that supplier's edit route.

**File or place:** `catalog/views.py`.

**Code or command from this step**

**Filename: `catalog/views.py`**

```python
@login_required
@role_required('manager')
def supplier_list(request):
    suppliers = Supplier.objects.all().order_by('name')
    return render(request, 'catalog/supplier_list.html', {
        'suppliers': suppliers,
        'edit_mode': request.GET.get('mode') == 'edit'
    })
```

**Understanding the code, one piece at a time**

`request.GET.get('mode') == 'edit'` is a UI-state switch, not authorization. Server-side Manager permissions remain enforced by decorators. The user verified editing and Cancel Edit.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 255](#step-255) · Next: [Step 257](#step-257)

---

<a id="step-257"></a>

### Step 257: Make Supplier rows selectable only in edit mode

**What was happening before this step?**

A single button needs an obvious way to choose which supplier to edit.

**What did we do, and what does it mean?**

The list template conditionally adds the `selectable` row class and the `row-link` anchor, using the supplier's ID in a named URL.

**File or place:** `catalog/templates/catalog/supplier_list.html`.

**Code or command from this step**

**Filename: `catalog/templates/catalog/supplier_list.html`**

```html
<tr {% if edit_mode %}class="selectable"{% endif %}>
    <td>
        {% if edit_mode %}
            <a class="row-link"
               href="{% url 'supplier_edit' supplier.id %}">
                {{ supplier.name }}
            </a>
        {% else %}
            {{ supplier.name }}
        {% endif %}
    </td>
</tr>
```

**Understanding the code, one piece at a time**

The actual final table also displays phone, email, address and Active/Inactive status. A click on the link navigates to the edit page; CSS makes the link cover the selectable row.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 256](#step-256) · Next: [Step 258](#step-258)

---

<a id="step-258"></a>

### Step 258: Create an external Catalog stylesheet

**What was happening before this step?**

The plain HTML table had cramped columns and lacked the consistent appearance of Employee Management. The user specifically requested external CSS rather than inline styling.

**What did we do, and what does it mean?**

We created an app-scoped static stylesheet for supplier actions, table borders, padding, hover effects and selection behavior.

**File or place:** `catalog/static/catalog/style.css`.

**Code or command from this step**

**Filename: `catalog/static/catalog/style.css`**

```css
.supplier-actions {
    display: flex;
    gap: 8px;
    margin-bottom: 24px;
}

.supplier-table {
    width: 100%;
    border-collapse: collapse;
}

.supplier-table th,
.supplier-table td {
    padding: 12px;
    border: 1px solid #dee2e6;
    text-align: left;
}

.supplier-table th {
    background-color: #f8f9fa;
}

.supplier-table tr:hover {
    background-color: #f5f5f5;
}

.selectable {
    position: relative;
    cursor: pointer;
}

.selectable:hover {
    background-color: #eeeeee;
}

.row-link::after {
    content: "";
    position: absolute;
    inset: 0;
}
```

**Understanding the code, one piece at a time**

Django static assets belong under `catalog/static/catalog/` to avoid clashes with similarly named files from other apps. The CSS file was later confirmed on GitHub.

**How did we know what happened?**

The change is present in the verified published source where applicable, and the user confirmed the corresponding local workflow during the session.

**Where to go next:** Previous: [Step 257](#step-257) · Next: [Step 259](#step-259)

---

<a id="step-259"></a>

### Step 259: Connect the CSS and diagnose the initial 404

**What was happening before this step?**

Buttons and badges appeared, but table borders and padding did not. Opening `/static/catalog/style.css` returned 404 even though the file existed.

**What did we do, and what does it mean?**

The template uses `{% load static %}` and the `extra_css` block to request the stylesheet. `findstatic` confirmed Django could discover the file. After restarting the development server, the user showed the styled table correctly rendered.

**File or place:** `catalog/templates/catalog/supplier_list.html`.

**Code or command from this step**

**Filename: `catalog/templates/catalog/supplier_list.html`**

```html
{% extends 'base.html' %}
{% load static %}

{% block title %}Manage Suppliers - Inventory System{% endblock %}

{% block extra_css %}
<link rel="stylesheet" href="{% static 'catalog/style.css' %}">
{% endblock %}

{% block content %}
<h2 class="mb-4">Manage Suppliers</h2>
<div class="supplier-actions">
    <a href="{% url 'supplier_create' %}" class="btn btn-primary">Add Supplier</a>
    {% if edit_mode %}
        <a href="{% url 'supplier_list' %}" class="btn btn-outline-secondary">Cancel Edit</a>
    {% else %}
        <a href="{% url 'supplier_list' %}?mode=edit" class="btn btn-outline-primary">Edit Supplier</a>
    {% endif %}
</div>
<div class="table-responsive">
    <table class="supplier-table">
        <!-- the existing supplier table header and rows remain here -->
    </table>
</div>
{% endblock %}
```

**Understanding the code, one piece at a time**

The excerpt shows the integration points; it is not a paste-ready replacement for the full table. The actual final template includes status badges, an empty state and Back to Home. `python manage.py findstatic catalog/style.css --verbosity 2` located the file. A restart resolved the observed symptom, but the evidence does not establish the underlying reason the running server initially returned 404.

**How did we know what happened?**

The user supplied the `findstatic` terminal output and a screenshot of the correctly styled list.

**Where to go next:** Previous: [Step 258](#step-258) · Next: [Step 260](#step-260)

---

<a id="step-260"></a>

### Step 260: Verify Chapter 3 and publish the code repository

**What was happening before this step?**

The feature was locally functional, but the published repository still needed the final edit and styling changes.

**What did we do, and what does it mean?**

The user confirmed the edit, cancel, create and styling workflows and reported `python manage.py check` with no issues. A single staged commit included all five Chapter 3 files and was pushed to `main`. We independently fetched the five published files and confirmed the latest GitHub commit.

**File or place:** `catalog/views.py; catalog/urls.py; catalog/templates/catalog/supplier_list.html; catalog/templates/catalog/supplier_form.html; catalog/static/catalog/style.css`.

**Code or command from this step**

**Filename: `catalog/views.py`**

**Filename: `catalog/urls.py`**

**Filename: `catalog/templates/catalog/supplier_list.html`**

**Filename: `catalog/templates/catalog/supplier_form.html`**

**Filename: `catalog/static/catalog/style.css`**

```powershell
git add -- catalog/views.py catalog/urls.py catalog/templates/catalog/supplier_list.html catalog/templates/catalog/supplier_form.html catalog/static/catalog/style.css
git diff --cached --check
git commit -m "Module 2 Chapter 3: Supplier editing and styling"
git push origin main
```

**Understanding the code, one piece at a time**

This block records the source publication command, not a command to rerun during study. Verified GitHub commit: https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/${src}. The system check and browser workflows are user-reported; no independent automated test suite was run in this journal update.

**How did we know what happened?**

The latest GitHub source commit and all five relevant files were inspected after the user confirmed the push.

**Where to go next:** Previous: [Step 259](#step-259) · Next: Chapter 4 (not started)

---

### End-to-end request flow

1. A Manager requests `/suppliers/`; `supplier_list` fetches Supplier rows and sets `edit_mode` from the query string.
2. Clicking Edit Supplier opens `/suppliers/?mode=edit`. The template shows selectable supplier names.
3. Clicking one name resolves the named `supplier_edit` route with its integer ID.
4. `supplier_edit` finds that record or returns 404, then builds `SupplierForm(instance=supplier)` for GET.
5. On POST, `SupplierForm(request.POST, instance=supplier)` validates and updates the existing row; success redirects to the list. Invalid input redisplays errors.
6. Cancel is a normal GET link, not a form submission. It does not save pending changes.

### Files and responsibilities

- `catalog/views.py`: list/create/edit request handling, Manager protection and GET/POST.
- `catalog/urls.py`: named list, create and supplier-ID edit routes.
- `catalog/templates/catalog/supplier_list.html`: single edit-mode button, selectable names, table and badges.
- `catalog/templates/catalog/supplier_form.html`: one form for new and existing suppliers; Update and Cancel in edit mode.
- `catalog/static/catalog/style.css`: external supplier-list and row-selection styles.

### What is verified and what is not

**Verified in GitHub:** the five files above on code `main` at commit `2b580fb`. **User-confirmed locally:** create and edit workflows, both Cancel interactions, table styling and `python manage.py check` without issues. **Not independently demonstrated:** automated tests for edit permissions, invalid input, non-existent IDs or static-file deployment in production. No Supplier deactivation workflow is claimed here.

### Questions you should be able to answer

1. Why does `instance=supplier` matter when saving an edit?
2. What does `get_object_or_404` do when a supplier ID is missing?
3. How is edit mode activated, and why is it not a security check?
4. Why do we keep the Manager decorator on the server-side view?
5. Why can Add and Edit share a ModelForm and template?
6. What is the difference between the form's Cancel link and Cancel Edit on the list?
7. Why are app-scoped static files and `{% load static %}` used?
8. What does `findstatic` prove, and what does it not prove?

**Chapter outcome:** Supplier editing is implemented and source-published. Continue to the next chapter only after this journal branch is updated and verified.
