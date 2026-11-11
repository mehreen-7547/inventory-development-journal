# Module 5 — Chapter 4: Purchase Order Management Interface

This chapter follows [Chapter 3](04-purchase-order-creation-interface.md). Interactive Step 488 begins the management work; journal steps continue at 353. The final source changes were published in commits [06694bb](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/06694bb) and [634c41e](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/634c41e).

A Manager can now create a purchase order with an automatically assigned number, open a list and detail page, mark a draft as ordered, or cancel an eligible order. Neither creation nor these status changes adjusts stock: goods receipt is a separate workflow.

## Step 353 — Add purchase-order list and detail routes

### Before this step
Managers could create orders, but there was no dedicated way to browse them or inspect their lines.

### What we did
Registered named URLs for the list, detail and status actions while retaining the existing creation route. The detail route takes the order's database primary key; the status route is a separate POST endpoint.

### Code
**CLASS — `purchasing/urls.py`**
```python
path('purchase-orders/', views.purchase_order_list, name='purchase_order_list'),
path('purchase-orders/<int:pk>/', views.purchase_order_detail, name='purchase_order_detail'),
path('purchase-orders/<int:pk>/status/', views.purchase_order_status, name='purchase_order_status'),
```

Named routes let templates and redirects refer to a stable name rather than hard-coding paths.

### How we checked it
The new management test suite resolves the list, detail and status URLs and accesses them through Django's test client.

### Next
Build the list and detail views.

## Step 354 — Display orders and their line totals

### Before this step
Order data existed in `PurchaseOrder` and `PurchaseOrderLine`, but the user had no dedicated order-history page.

### What we did
Added a Manager-only list view with supplier and creator relationships and a count of lines. The detail view loads the selected order, multiplies each ordered quantity by its unit cost and accumulates the order total.

### Code
**CLASS — `purchasing/views.py`**
```python
orders = PurchaseOrder.objects.select_related(
    'supplier', 'created_by'
).annotate(
    product_count=Count('lines')
).order_by('-created_at', '-pk')
```

**CLASS — `purchasing/views.py`**
```python
for line in lines:
    line_total = line.quantity_ordered * line.unit_cost
    order_total += line_total
    order_items.append({'line': line, 'line_total': line_total})
```

`select_related` fetches linked supplier and user details efficiently. The total uses the order line's purchase cost, not the product's catalogue selling price.

### How we checked it
`test_manager_can_view_list` checks order and supplier text; `test_manager_can_view_detail` checks the product and a calculated ₹100 total.

### Next
Connect the templates and navigation.

## Step 355 — Make purchase-order rows open their details

### Before this step
A display-only list was inconvenient; the user wanted the entire row clickable instead of a separate View button.

### What we did
Added the list and detail templates. Each list row stores the detail URL and responds to mouse clicks and Enter/Space. The detail template shows supplier, status, creator, date, product lines and totals.

### Code
**NEW — `purchasing/templates/purchasing/purchase_order_list.html`**
```django
<tr class="po-row" tabindex="0" role="link"
    data-url="{% url 'purchase_order_detail' order.pk %}"
    style="cursor: pointer;">
```

**CLASS — `purchasing/templates/purchasing/purchase_order_list.html`**
```javascript
row.addEventListener('click', openOrder);
row.addEventListener('keydown', function (event) {
    if (event.key === 'Enter' || event.key === ' ') {
        event.preventDefault();
        openOrder();
    }
});
```

The row's URL comes from Django's URL resolver. Keyboard support means the same navigation is not mouse-only.

### How we checked it
The user reported the revised browser behavior working during Chapter 4 verification.

### Next
Implement controlled status transitions.

## Step 356 — Change eligible order statuses without changing stock

### Before this step
New purchase orders remained in Draft, and there was no Manager action to mark them ordered or cancelled.

### What we did
Added `change_purchase_order_status` to the service layer, a POST-only status view, and action forms on the detail page. Allowed transitions are Draft → Ordered, Draft → Cancelled and Ordered → Cancelled. Received and partially received states are reserved for goods receipt.

### Code
**CLASS — `purchasing/views.py`**
```python
@login_required
@require_POST
def purchase_order_status(request, pk):
    if not manager_allowed(request.user):
        messages.error(request, 'Only active managers can change order status.')
        return redirect('home')
    new_status = request.POST.get('status', '')
```

**NEW — `purchasing/templates/purchasing/purchase_order_detail.html`**
```django
<form method="post" action="{% url 'purchase_order_status' order.pk %}">
    {% csrf_token %}
    <input type="hidden" name="status" value="ordered">
    <button type="submit" class="btn btn-primary">Mark as Ordered</button>
</form>
```

The service validates the transition inside a database transaction. Status changes alone do not call the stock movement service.

### How we checked it
Tests cover Draft → Ordered, cancellation from Draft and Ordered, refusal to reorder a cancelled order, refusal to cancel a received order, and no stock change.

### Next
Improve order creation and product context.

## Step 357 — Generate PO numbers and show product stock context

### Before this step
The creation page asked for a PO number even though Managers should not need to invent one. It also lacked the stock information needed to choose quantities.

### What we did
Made the form's PO number field optional and hidden. For normal browser submissions the view generates a `PO-` prefix plus 12 uppercase hexadecimal characters; explicitly supplied numbers remain supported for existing tests. The view also sends current stock, safety stock and lead time per product to the template.

### Code
**CLASS — `purchasing/forms.py`**
```python
po_number = forms.CharField(
    max_length=20, required=False, widget=forms.HiddenInput()
)
```

**CLASS — `purchasing/views.py`**
```python
po_number = supplied_number or (
    'PO-' + uuid4().hex[:12].upper()
)
```

**CLASS — `purchasing/views.py`**
```python
product_details = {
    str(product.pk): {
        'on_hand': product.on_hand,
        'safety_stock': product.safety_stock,
        'lead_time_days': product.lead_time_days,
        'unit_price': str(product.unit_price)
    }
    for product in available_products
}
```

This number is automatically assigned when saving, not reserved before submission. The product details are informational; they do not change inventory.

### How we checked it
`test_auto_generated_po_number` checks prefix, length, saved line and redirect to detail. The user confirmed numbers appeared after saving.

### Next
Finish dynamic dropdown and total calculations.

## Step 358 — Prevent duplicate product selections and calculate totals

### Before this step
The original creation template filtered by supplier but still displayed a product already selected in another row. It also did not render stock, safety stock, lead time or calculated totals.

### What we did
Replaced the creation template with stock-context fields, per-line totals and an order total. The JavaScript rebuilds each product dropdown using the selected supplier and products selected in other rows; it preserves that row's own selection. Adding, removing or changing items recalculates the display. Backend duplicate-product validation remains in the Django form.

### Code
**CLASS — `purchasing/templates/purchasing/purchase_order_form.html`**
```django
{{ product_suppliers|json_script:"product-supplier-data" }}
{{ product_details|json_script:"product-details-data" }}
```

**CLASS — `purchasing/templates/purchasing/purchase_order_form.html`**
```javascript
const selectedElsewhere = new Set(
    selections.filter(function (value, otherIndex) {
        return otherIndex !== index && value !== '';
    })
);
```

**CLASS — `purchasing/templates/purchasing/purchase_order_form.html`**
```javascript
const lineTotal = validQuantity && validCost ? quantity * cost : 0;
row.querySelector('.line-total').textContent = money(lineTotal);
```

The dropdown exclusion is a usability improvement, not a substitute for server-side validation. Unit cost is entered by the Manager; the displayed total is recalculated in the browser.

### How we checked it
The user reported all targeted browser checks passed, including supplier filtering, product exclusion, live stock context, totals, removal and supplier switching.

### Next
Add regression tests for management features.

## Step 359 — Test purchase-order management and repair test discovery

### Before this step
The initial 39 purchasing tests did not cover the new list, detail, status and automatic numbering functionality. The new test file was initially saved as `est_purchase_management.py`, so Django did not discover it.

### What we did
Renamed the file to `test_purchase_management.py`. Its 17 tests cover Manager and Sales permissions, detail totals, status transitions, POST-only changes, stock preservation, automatic numbering, stock context and dashboard navigation.

### Code
**NEW — `purchasing/test_purchase_management.py`**
```python
def test_auto_generated_po_number(self):
    self.client.force_login(self.manager)
    response = self.client.post(
        reverse('purchase_order_create'),
        {
            'supplier': self.supplier.pk,
            'product_1': self.product.pk,
            'quantity_1': '3',
            'unit_cost_1': '20.00'
        }
    )
    self.assertEqual(response.status_code, 302)
```

The filename must begin with `test` for Django's normal test discovery. A successful HTTP redirect is only one assertion; the same test also checks the saved PO number and order line.

### How we checked it
Before the rename, 39 tests passed. After the rename, 56 tests were discovered; 55 passed and one dashboard-link assertion failed, leading to the final correction.

### Next
Fix dashboard navigation, verify and publish.

## Step 360 — Add the dashboard link and publish Chapter 4

### Before this step
The 56th test expected a Purchase Orders link on the Manager dashboard, but the template still contained only Create Purchase Order. The first source commit did not include the dashboard file.

### What we did
Added a separate Purchase Orders button to the Manager dashboard and published it in a follow-up source commit. The main management changes were committed and pushed first as `06694bb`; the dashboard correction was pushed as `634c41e`.

### Code
**CLASS — `core/templates/core/home.html`**
```django
<a href="{% url 'purchase_order_list' %}"
   class="btn btn-outline-primary">
    Purchase Orders
</a>
```

This link belongs in the Manager branch of the role-based dashboard, not the Sales Staff branch.

### How we checked it
The user showed `python manage.py check` with zero issues, 39 initial tests passing and a clean `git diff --check`. After fixing test discovery, the user showed 56 discovered with only the missing dashboard link failing. GitHub confirms both source commits are on `main` and the dashboard template now contains the link. **The final 56/56 test output was not supplied**, so it must not be claimed as independently verified.

### Next
Proceed to Module 5 Chapter 5, covering goods receipt and stock updates only after the approved workflow is reviewed.
