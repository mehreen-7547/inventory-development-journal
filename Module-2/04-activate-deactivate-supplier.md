# MODULE 2 — Supplier and inventory foundations

## Chapter 4: Activate and Deactivate Supplier (Steps 261–269)

### Before this chapter
Chapter 3 finished Supplier editing. The Supplier model already contained `is_active`, and the list already displayed Active/Inactive badges, but Managers could not change that field from the application. The Inventory Manager dashboard also lacked a Manage Suppliers link.

### What we built
Managers can open **Manage Suppliers → Edit Supplier → choose a supplier**, then deactivate or reactivate it on a separate POST form. The supplier is not deleted. The list continues to show status badges but no Action column. The dashboard provides a direct link, and six automated tests cover changes, permissions, request methods, and persistence.

### Why this design?
Think of the supplier record as a contact card with an **available/not available** switch. Turning the switch off keeps the card for historical reference. Employee Management already put its account-status switch on the edit page, so we reused that UI pattern instead of adding a status button in every table row.

> **Numbering:** Interactive development Steps **399–403** are recorded here as continuous journal Steps **261–269**. These are different numbering systems. Interactive Steps 399–401 initially separated view, URL and template changes; Step 401 was revised after checking Employee Management; Step 402 completed UI and dashboard integration; Step 403 added tests. The user subsequently requested that future interactive steps cover one end-to-end feature rather than one file per response.
>
> **Evidence:** Verified source commit [`1c3f0ec`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/1c3f0ecb2474f3285c0b529fcf00fb67c9e6ab0f), branch `main`. The user reported `python manage.py check`, six passing `catalog` tests, and browser verification. GitHub source was independently inspected; no claim is made that we executed Django tests ourselves.

### Jump to a step
- [Step 261: Add the Manager-only POST status view](#step-261)
- [Step 262: Register the status URL](#step-262)
- [Step 263: Correct the UI placement to match Employee Management](#step-263)
- [Step 264: Put a separate status form on Edit Supplier](#step-264)
- [Step 265: Expose Manage Suppliers on the Manager dashboard](#step-265)
- [Step 266: Create a repeatable supplier-status test fixture](#step-266)
- [Step 267: Test both status transitions and database persistence](#step-267)
- [Step 268: Test denied requests and POST-only protection](#step-268)
- [Step 269: Verify and publish Chapter 4](#step-269)

---

<a id="step-261"></a>

### Step 261: Add the Manager-only POST status view

**Before this step:** The supplier status was visible but not editable.

**What we did and why:** Supplier records already had an is_active Boolean, but there was no action to change it. This view finds one saved supplier, flips its Boolean, saves only that field, and returns to the list. @login_required and @role_required('manager') protect the action; @require_POST rejects GET with HTTP 405. No new model or migration was necessary.

**Exact file(s):** `catalog/views.py`

**Code or command (file named above):**

**Filename: `catalog/views.py`**

```python
from django.views.decorators.http import require_POST

@login_required
@role_required('manager')
@require_POST
def toggle_supplier_status(request, supplier_id):
    supplier = get_object_or_404(Supplier, id=supplier_id)
    supplier.is_active = not supplier.is_active
    supplier.save(update_fields=['is_active'])
    return redirect('supplier_list')
```

**How we checked it:** The user added the view and reported python manage.py check succeeded. The published view matches the described behavior.

**Next:** Previous: [Step 260](03-edit-supplier.md#step-260) · Next: [Step 262](#step-262)

---

<a id="step-262"></a>

### Step 262: Register the status URL

**Before this step:** The previous step established the foundation for this change.

**What we did and why:** A view is not reachable until Django maps a URL to it. The integer segment passes the chosen supplier ID to the view. The named route allows {% url %} in the template instead of hardcoded paths. The view—not the URL—enforces the role and HTTP-method checks.

**Exact file(s):** `catalog/urls.py`

**Code or command (file named above):**

**Filename: `catalog/urls.py`**

```python
path(
    'suppliers/<int:supplier_id>/status/',
    views.toggle_supplier_status,
    name='toggle_supplier_status'
),
```

**How we checked it:** The user reported python manage.py check succeeded after adding this route; the final published URL exists.

**Next:** Previous: [Step 261](#step-261) · Next: [Step 263](#step-263)

---

<a id="step-263"></a>

### Step 263: Correct the UI placement to match Employee Management

**Before this step:** The initial table-button proposal did not match the existing Employee Management interface.

**What we did and why:** The initial suggestion was an Action column with a button on every supplier row. The user asked whether employee rows worked this way. Inspection of the real Employee List and Edit Employee templates showed that status buttons belong on the edit page. We withdrew the per-row instruction and kept the supplier list as five columns. This is a correction of an earlier design proposal, not a separate new feature.

**Exact file(s):** `templates/accounts/edit_employee.html and catalog/templates/catalog/supplier_list.html`

**Code or command (file named above):**

**Filename: `templates/accounts/edit_employee.html and catalog/templates/catalog/supplier_list.html`**

```html
<!-- Employee edit-page pattern verified in:
     templates/accounts/edit_employee.html -->

<!-- Supplier list keeps only its status display: -->
<td>
    {% if supplier.is_active %}
        <span class="badge bg-success">Active</span>
    {% else %}
        <span class="badge bg-secondary">Inactive</span>
    {% endif %}
</td>
```

**How we checked it:** The user showed a screenshot of a local six-column table while correcting the design. The final published Supplier List has five columns, no Action column, and colspan=5 for an empty list.

**Next:** Previous: [Step 262](#step-262) · Next: [Step 264](#step-264)

---

<a id="step-264"></a>

### Step 264: Put a separate status form on Edit Supplier

**Before this step:** The previous step established the foundation for this change.

**What we did and why:** The template is shared by Add Supplier and Edit Supplier. form.instance.pk exists only for a saved supplier, so a new supplier never sees the status controls. The status POST form sits after the closing tag of the details-edit form; nested HTML forms would be invalid. CSRF protects the POST. Changing status does not submit or overwrite the supplier's contact details.

**Exact file(s):** `catalog/templates/catalog/supplier_form.html`

**Code or command (file named above):**

**Filename: `catalog/templates/catalog/supplier_form.html`**

```html
{% if form.instance.pk %}
    <hr>
    <h5 class="mt-4">Supplier Status</h5>
    <p>
        Current status:
        {% if form.instance.is_active %}
            <span class="badge bg-success">Active</span>
        {% else %}
            <span class="badge bg-secondary">Inactive</span>
        {% endif %}
    </p>
    <form method="POST"
          action="{% url 'toggle_supplier_status' form.instance.pk %}"
          class="mb-4">
        {% csrf_token %}
        {% if form.instance.is_active %}
            <button type="submit" class="btn btn-outline-danger">
                Deactivate Supplier
            </button>
        {% else %}
            <button type="submit" class="btn btn-outline-success">
                Reactivate Supplier
            </button>
        {% endif %}
    </form>
{% endif %}
```

**How we checked it:** The published template contains the exact conditional status section and a separate form. The user reported the browser deactivate/reactivate flow worked.

**Next:** Previous: [Step 263](#step-263) · Next: [Step 265](#step-265)

---

<a id="step-265"></a>

### Step 265: Expose Manage Suppliers on the Manager dashboard

**Before this step:** Managers still needed to type the supplier URL manually.

**What we did and why:** Previously the Manager dashboard had only its heading, forcing the user to type /suppliers/ manually. The named link now opens the supplier list. Its placement inside the manager role branch makes it visible to Inventory Managers. This is navigation, not the security boundary: supplier_list itself remains role-protected.

**Exact file(s):** `core/templates/core/home.html`

**Code or command (file named above):**

**Filename: `core/templates/core/home.html`**

```html
{% elif request.user.role == 'manager' %}
    <h3>Inventory Manager Dashboard</h3>
    <p>
        <a href="{% url 'supplier_list' %}"
           class="btn btn-primary">
            Manage Suppliers
        </a>
    </p>
```

**How we checked it:** The published dashboard includes the Manager-only link. The user reported it worked in the browser.

**Next:** Previous: [Step 264](#step-264) · Next: [Step 266](#step-266)

---

<a id="step-266"></a>

### Step 266: Create a repeatable supplier-status test fixture

**Before this step:** The feature had only been checked interactively; no Catalog test suite existed.

**What we did and why:** setUp creates the same starting conditions before each test: a Manager, Sales Staff user, and active supplier. reverse resolves the URL by its name and supplier ID. These are test records in Django's isolated test database, not permanent real suppliers.

**Exact file(s):** `catalog/tests.py`

**Code or command (file named above):**

**Filename: `catalog/tests.py`**

```python
from django.test import TestCase
from django.urls import reverse
from accounts.models import CustomUser
from .models import Supplier

class SupplierStatusTests(TestCase):
    def setUp(self):
        self.manager = CustomUser.objects.create_user(
            username='testmanager', email='manager@example.com',
            password='ManagerPass123!', role='manager'
        )
        self.sales = CustomUser.objects.create_user(
            username='testsales', email='sales@example.com',
            password='SalesPass123!', role='sales'
        )
        self.supplier = Supplier.objects.create(
            name='Test Supplier', is_active=True
        )
        self.status_url = reverse(
            'toggle_supplier_status', args=[self.supplier.id]
        )
```

**How we checked it:** The complete fixture is present in the published catalog/tests.py.

**Next:** Previous: [Step 265](#step-265) · Next: [Step 267](#step-267)

---

<a id="step-267"></a>

### Step 267: Test both status transitions and database persistence

**Before this step:** The previous step established the foundation for this change.

**What we did and why:** force_login sets up a Manager session without typing into the login form. client.post simulates clicking the status form. refresh_from_db is necessary to reload the actual saved value; checking the old in-memory object could give a misleading result. The third test demonstrates soft deactivation: the supplier row still exists.

**Exact file(s):** `catalog/tests.py`

**Code or command (file named above):**

**Filename: `catalog/tests.py`**

```python
def test_manager_can_deactivate_supplier(self):
    self.client.force_login(self.manager)
    response = self.client.post(self.status_url)
    self.supplier.refresh_from_db()
    self.assertFalse(self.supplier.is_active)
    self.assertRedirects(response, reverse('supplier_list'))

def test_manager_can_reactivate_supplier(self):
    self.supplier.is_active = False
    self.supplier.save()
    self.client.force_login(self.manager)
    response = self.client.post(self.status_url)
    self.supplier.refresh_from_db()
    self.assertTrue(self.supplier.is_active)
    self.assertRedirects(response, reverse('supplier_list'))

def test_deactivated_supplier_remains_in_database(self):
    self.client.force_login(self.manager)
    self.client.post(self.status_url)
    self.assertTrue(Supplier.objects.filter(id=self.supplier.id).exists())
```

**How we checked it:** These three tests are present in the published test class. The user reported all six catalog tests passed locally.

**Next:** Previous: [Step 266](#step-266) · Next: [Step 268](#step-268)

---

<a id="step-268"></a>

### Step 268: Test denied requests and POST-only protection

**Before this step:** The previous step established the foundation for this change.

**What we did and why:** A page hiding a button is not enough to enforce permissions. These tests send requests directly to the route. GET must return 405 without changing the supplier. A signed-in Sales Staff user is redirected home without changing the supplier. A visitor who has not logged in is redirected, again without changing the record. These tests verify different failure paths, not just the happy path.

**Exact file(s):** `catalog/tests.py`

**Code or command (file named above):**

**Filename: `catalog/tests.py`**

```python
def test_get_request_cannot_change_status(self):
    self.client.force_login(self.manager)
    response = self.client.get(self.status_url)
    self.supplier.refresh_from_db()
    self.assertEqual(response.status_code, 405)
    self.assertTrue(self.supplier.is_active)

def test_sales_staff_cannot_change_status(self):
    self.client.force_login(self.sales)
    response = self.client.post(self.status_url)
    self.supplier.refresh_from_db()
    self.assertTrue(self.supplier.is_active)
    self.assertRedirects(response, reverse('home'))

def test_unauthenticated_user_cannot_change_status(self):
    response = self.client.post(self.status_url)
    self.supplier.refresh_from_db()
    self.assertTrue(self.supplier.is_active)
    self.assertEqual(response.status_code, 302)
```

**How we checked it:** All three tests are in the published source; the user reported the six-test catalog suite passed.

**Next:** Previous: [Step 267](#step-267) · Next: [Step 269](#step-269)

---

<a id="step-269"></a>

### Step 269: Verify and publish Chapter 4

**Before this step:** The implementation and test cases were ready for a final chapter checkpoint.

**What we did and why:** The user confirmed Django's system check, six automated tests and browser workflow. Git reported a trailing-whitespace warning in core/templates/core/home.html during diff --cached --check; because the commands were run separately in PowerShell, commit and push still succeeded. Git also printed a line-ending warning (LF/CRLF). Neither warning proves a runtime failure, but the whitespace can be cleaned up later. The final source commit changes exactly six files. The supplier list has no row-level status buttons.

**Exact file(s):** `catalog/views.py; catalog/urls.py; catalog/templates/catalog/supplier_form.html; catalog/templates/catalog/supplier_list.html; catalog/tests.py; core/templates/core/home.html`

**Code or command (file named above):**

**Filename: `catalog/views.py; catalog/urls.py; catalog/templates/catalog/supplier_form.html; catalog/templates/catalog/supplier_list.html; catalog/tests.py; core/templates/core/home.html`**

```powershell
python manage.py check
python manage.py test catalog

git add catalog/views.py catalog/urls.py catalog/templates/catalog/supplier_form.html catalog/templates/catalog/supplier_list.html catalog/tests.py core/templates/core/home.html
git diff --cached --check
git commit -m "Module 2 Chapter 4: Supplier activation and deactivation"
git push origin main
```

**How we checked it:** GitHub main commit 1c3f0ecb2474f3285c0b529fcf00fb67c9e6ab0f was independently fetched and all six published files were inspected. Local test results were reported by the user, not rerun by the journal author.

**Next:** Previous: [Step 268](#step-268) · Chapter 4 complete. Continue only after journal verification.

---

## End-to-end workflow

1. Sign in as an Inventory Manager and open **Manage Suppliers** from the dashboard.
2. Select **Edit Supplier** on the list, then click a supplier name.
3. The shared form shows contact details and, because the supplier has a primary key, the **Supplier Status** section.
4. Click **Deactivate Supplier**. The browser sends a CSRF-protected POST to the named status route.
5. The Manager-only view finds that supplier, flips `is_active`, saves the field, and redirects to the list.
6. The list badge reads **Inactive**; the record is still present. Reopening the edit page now shows **Reactivate Supplier**.
7. Add Supplier has no status section because its ModelForm instance has no primary key yet.

## File responsibilities

- `catalog/models.py`: existing Supplier model with `is_active`; unchanged this chapter.
- `catalog/views.py`: checks Manager access, POST method, fetches and updates one supplier.
- `catalog/urls.py`: names the supplier-specific status endpoint.
- `catalog/templates/catalog/supplier_form.html`: shows the separate status form only for existing suppliers.
- `catalog/templates/catalog/supplier_list.html`: shows status badges and five columns, not per-row status actions.
- `core/templates/core/home.html`: Manager dashboard navigation.
- `catalog/tests.py`: six repeatable behavioral and security tests.

## Verified versus not verified

**Verified on GitHub:** All six files in source commit `1c3f0ec`; status route, view, edit-page form, list layout, dashboard link, test definitions.

**Reported locally by the user:** Django system check passed; all six Catalog tests passed; browser deactivate/reactivate and dashboard navigation worked.

**Not independently executed:** Django tests or browser automation in the journal-writing environment. The test suite does not yet prove future behavior involving purchase orders, products or supplier reassignment because those workflows are not implemented here.

## Questions an instructor may ask

**Why not delete inactive suppliers?** Deactivation preserves the record and allows reactivation. Later workflows can decide how inactive suppliers should affect new product assignments.

**Why use POST instead of GET?** Viewing a URL should not change database state. `@require_POST` prevents accidental GET-based changes.

**Why check the Manager role in the view?** UI controls can be bypassed by direct HTTP requests. Authorization must happen on the server.

**Why have two forms on the edit page?** One saves supplier details; the other changes status. Keeping them separate prevents one action from submitting unrelated fields.

**Why call `refresh_from_db()` in tests?** It reloads the actual saved database value after a request.

**Why did the initial Action column disappear?** The user wanted parity with Employee Management, which puts status controls on the edit page rather than in each table row.

## Chapter checkpoint

Source commit: [`1c3f0ecb2474f3285c0b529fcf00fb67c9e6ab0f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/1c3f0ecb2474f3285c0b529fcf00fb67c9e6ab0f). Next development work starts only after this journal is updated and verified.
