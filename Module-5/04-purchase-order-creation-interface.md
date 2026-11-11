# Purchase Order Creation Interface — Module 5 Chapter 3

Interactive development Steps **480–487** correspond to journal Steps **345–352**. This chapter follows the Sales UI maintenance checkpoint (journal Steps 343–344). The original source commit is [`f3063b6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/f3063b6).

## Chapter introduction

The purchase-order models and creation service already existed. This chapter adds the Manager-facing Django form and browser workflow. A purchase order can contain multiple products, but every product must belong to its selected supplier. Creating an order records an intention to buy; it does **not** increase inventory. The browser's Add Item button is convenient, but the Django form and service still validate everything on the server.

## Step 345 — Create the purchase-order form

### Before this step
There was a tested purchase-order service, but no HTML form for Managers to enter an order.

### What we did
Created `purchasing/forms.py` with a `PurchaseOrderForm` using a purchase-order number, an active supplier, and product, quantity and unit-cost inputs. The initial form used one product and was later generalized.

### Code
**NEW — `purchasing/forms.py`**
```python
class PurchaseOrderForm(forms.Form):
    MAX_ROWS = 100

    po_number = forms.CharField(max_length=20)
    supplier = forms.ModelChoiceField(queryset=Supplier.objects.none())
```
`ModelChoiceField` validates submitted IDs against a database queryset. The active-supplier queryset is assigned in `__init__`, rather than at module import time.

### How we checked it
The user initially reported `python manage.py check` with zero issues. After connecting routes, a missing `purchasing.forms` import was diagnosed and the file was created in the app folder.

### Next
Connect the form to the existing service.

## Step 346 — Connect the Manager-only creation view

### Before this step
A form by itself cannot persist a purchase order; the service already contains the transactional rules.

### What we did
Added `purchase_order_create` in `purchasing/views.py`. It checks authentication and active Manager role, validates POST data, calls `create_purchase_order`, displays validation errors and redirects after success. The initial view handled one line; Step 350 updated it for multiple lines.

### Code
**CLASS — `purchasing/views.py`**
```python
@login_required
def purchase_order_create(request):
    if not request.user.is_active or request.user.role != 'manager':
        messages.error(request, 'Only active managers can create purchase orders.')
        return redirect('home')

    form = PurchaseOrderForm(request.POST) if request.method == 'POST' else PurchaseOrderForm()
```
The final view passes `form.cleaned_data['items']` to the service. Server-side permission checks are required even when the dashboard hides links.

### How we checked it
The form page was later opened successfully as a Manager; final purchasing tests passed.

### Next
Add the URL and HTML template.

## Step 347 — Create the purchase-order route and form page

### Before this step
The view was not yet reachable from a browser.

### What we did
Created `purchasing/urls.py` and `purchasing/templates/purchasing/purchase_order_form.html`. The page displays errors, supplier selection, product rows, a submit button, and the warning that ordering does not receive stock.

### Code
**NEW — `purchasing/urls.py`**
```python
from django.urls import path
from . import views

urlpatterns = [
    path('add/', views.purchase_order_create, name='purchase_order_create'),
]
```
**NEW — `purchasing/templates/purchasing/purchase_order_form.html`**
```django
<form method="post" id="purchase-order-form">
    {% csrf_token %}
    {% if form.non_field_errors %}
        <div class="alert alert-danger">{{ form.non_field_errors }}</div>
    {% endif %}
```
`csrf_token` protects POST requests; the form displays validation feedback rather than silently discarding bad input.

### How we checked it
The user provided a screenshot confirming the page loaded and rendered its controls.

### Next
Connect the route to the project and dashboard.

## Step 348 — Register purchasing URLs and add dashboard navigation

### Before this step
The app-specific URL file was not included by the project URL resolver, and Managers lacked a dashboard link.

### What we did
Added purchasing URLs to `config/urls.py` and a Manager-only Create Purchase Order link in `core/templates/core/home.html`. No Sales Staff link was added.

### Code
**CLASS — `config/urls.py`**
```python
path('', include('purchasing.urls')),
```
**CLASS — `core/templates/core/home.html`**
```django
<a href="{% url 'purchase_order_create' %}" class="btn btn-outline-primary">
    Create Purchase Order
</a>
```
URL `include` delegates matching to the app; the named URL keeps templates independent of hard-coded paths.

### How we checked it
Initial system checks revealed the missing `purchasing/forms.py` file; after it was created, the user confirmed the browser page.

### Next
Filter products by supplier.

## Step 349 — Filter products to the chosen supplier

### Before this step
The product dropdown initially included items from all suppliers, which made incorrect combinations easy to select.

### What we did
The view sends a mapping of product IDs to supplier IDs. JavaScript filters each dropdown when the supplier changes, while Django independently rejects mismatched products on POST.

### Code
**CLASS — `purchasing/views.py`**
```python
product_suppliers = {
    str(product.pk): product.supplier_id
    for product in available_products
}
```
**CLASS — `purchasing/templates/purchasing/purchase_order_form.html`**
```django
{{ product_suppliers|json_script:"product-supplier-data" }}
```
`json_script` safely places server data into the HTML. The browser filters choices for usability; the backend is the security boundary.

### How we checked it
The later 39-test purchasing suite included server-side rejection of a product from another supplier. The user confirmed the interface changes were done.

### Next
Allow any needed number of item rows rather than fixed boxes.

## Step 350 — Add dynamic Add Item and Remove controls

### Before this step
One line per order was insufficient, but a fixed collection of empty product boxes was unwanted.

### What we did
Changed `PurchaseOrderForm` to discover indexed `product_N`, `quantity_N` and `unit_cost_N` POST keys, construct fields dynamically, and validate complete, nonduplicate rows. The view builds `order_rows` and passes a `next_row_number`; the template adds/removes rows using a `<template>` element. Maximum 100 rows.

### Code
**CLASS — `purchasing/forms.py`**
```python
for key in self.data.keys():
    if key.startswith(('product_', 'quantity_', 'unit_cost_')):
        number = key.rsplit('_', 1)[1]
        if number.isdigit() and int(number) > 0:
            row_numbers.add(int(number))
```
**CLASS — `purchasing/templates/purchasing/purchase_order_form.html`**
```javascript
const html = template.innerHTML.replaceAll('__INDEX__', String(nextIndex));
container.insertAdjacentHTML('beforeend', html);
nextIndex += 1;
filterProducts(false);
updateButtons();
```
The numbered field names let Django reconstruct the same rows after an invalid submission. Removed row numbers need not be consecutive. The service saves every validated item in one transaction.

### How we checked it
The user confirmed the multi-item interface was implemented. Later tests verified multiple lines, sparse indices, incomplete rows and duplicate rejection.

### Next
Add focused automated tests.

## Step 351 — Test multi-product creation and validation

### Before this step
The new browser behavior required backend regression coverage; visual inspection alone could not prove database consistency.

### What we did
Extended `purchasing/tests.py` with `PurchaseOrderFormViewTests` covering Manager access, Sales Staff/anonymous denial, two products under one order, unchanged stock, supplier mismatch, duplicates, incomplete rows, blank rows, sparse indices, invalid POST retention, 100-row limit, duplicate order number and inactive products.

### Code
**CLASS — `purchasing/tests.py`**
```python
def test_multiple_products_saved_in_one_order(self):
    self.client.force_login(self.manager)
    response = self.client.post(self.url, self.data)
    self.assertRedirects(response, reverse('home'))
    order = PurchaseOrder.objects.get(po_number='PO-FORM-001')
    self.assertEqual(order.lines.count(), 2)
```
This test exercises the complete HTTP → form → service → database path, not merely the form in isolation.

### How we checked it
User ran `python manage.py test purchasing`: **39 tests found, 39 passed**, in 84.153 seconds; zero system-check issues.

### Next
Run final checks and publish the chapter.

## Step 352 — Verify, inspect Sales correction and publish Chapter 3

### Before this step
The working tree included the purchasing changes plus a separate Sales JavaScript parser fix; that additional change needed inspection before committing.

### What we did
Ran Django system check, inspected Git status and whitespace, verified the Sales diff contained only the intended `nextIndex` conversion, then committed and pushed all eight changed/new paths to source `main`. No model migrations were required.

### Code
**CLASS — `sales/templates/sales/sale_form.html`**
```javascript
let nextIndex = Number('{{ next_row_number }}');
```
The quoted Django interpolation is valid JavaScript source for the editor and converts the rendered row number to a number. This is a minor Sales maintenance correction included in the chapter's source commit.

**Source commit:** [`f3063b6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/f3063b6).

### How we checked it
`python manage.py check`: zero issues; `git diff --check`: no whitespace errors (only Windows LF/CRLF notices); `python manage.py test purchasing`: 39/39 passed. The user reported successful `git push origin main`, commit `f3063b6`. The final `git status --short` command produced no additional output in the supplied transcript.

### Next
Proceed to Module 5 Chapter 4; goods receipt and order status changes remain future work.
