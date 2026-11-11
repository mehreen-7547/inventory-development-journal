# Module 5 — Chapter 5: Goods Receipt and Close Short

This chapter follows [Chapter 4](05-purchase-order-management-interface.md). It documents the locally implemented Chapter 5 code supplied in `purchasing.zip`, and the user's recorded test results. Source files have **not yet been pushed** at this checkpoint.

A Manager can record partial or complete supplier deliveries, distinguish damaged goods from usable stock, prevent over-receipt and repeated submissions, and close a partially received purchase order short without inventing stock.

## Step 361 — Extend the purchase order models

### Before this step
A purchase order stored ordered quantities but could not track separate deliveries or damaged units.

### What we did
Added `closed_short`, cumulative `quantity_received`, and `GoodsReceipt`/`GoodsReceiptLine` records. The receipt records who accepted a delivery, when, which order it belongs to, and a unique submission key.

### Code
**CLASS — `purchasing/models.py`**
```python
quantity_received = models.PositiveIntegerField(default=0)

class GoodsReceipt(models.Model):
    receipt_number = models.CharField(max_length=20, unique=True)
    purchase_order = models.ForeignKey(PurchaseOrder, on_delete=models.PROTECT, null=True, blank=True)
    received_by = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.PROTECT)
    received_at = models.DateTimeField(auto_now_add=True)
    note = models.TextField(blank=True)
    submission_key = models.CharField(max_length=100, unique=True)
```

`quantity_received` on an order line counts both usable and damaged delivered units. Only usable goods increase inventory. `PROTECT` preserves references to historical receipts.

### How we checked it
`makemigrations purchasing`, `migrate`, and `check` succeeded; migration `purchasing/migrations/0002_purchaseorderline_quantity_received_and_more.py` was created.

### Next
Implement atomic receipt processing.

## Step 362 — Implement atomic receipt processing

### Before this step
The models existed but recording a delivery still needed to validate quantities, prevent duplicate submissions, and update inventory consistently.

### What we did
Implemented `receive_goods` as a Manager-only service wrapped in `transaction.atomic()`. It locks the order and its lines, checks outstanding quantities, creates a receipt and receipt lines, and posts usable stock through `change_stock`.

### Code
**CLASS — `purchasing/services.py`**
```python
with transaction.atomic():
    existing = GoodsReceipt.objects.filter(
        submission_key=submission_key
    ).first()

    if existing:
        if existing.purchase_order_id != order_id:
            raise ValueError('Submission key already used.')
        if existing.received_by_id != user.pk:
            raise ValueError('Submission key already used.')
        return existing
```

The idempotency key prevents a repeated form submission from adding stock twice. The service rejects over-receipt and duplicate product entries; its transaction rolls back if a later operation fails.

### How we checked it
`purchasing.test_goods_receipt` passed all five service tests.

### Next
Test partial, damaged, full and repeated receipts.

## Step 363 — Test partial, damaged, full and repeated receipts

### Before this step
A passing database migration does not establish that inventory quantities are correct.

### What we did
Added a dedicated test file with fixtures for a Manager, supplier, product, opening stock and ordered PO. Tests cover partial delivery, damaged units, full delivery, over-receipt rejection and repeat submission.

### Code
**NEW — `purchasing/test_goods_receipt.py`**
```powershell
python manage.py test purchasing.test_goods_receipt
```

The fixture creates opening stock using `inventory.services.change_stock`, because `Product.on_hand` is maintained through stock movements rather than edited directly.

### How we checked it
Five tests passed in 1.817 seconds with zero Django system-check issues.

### Next
Build the receipt entry form.

## Step 364 — Build the receipt entry form

### Before this step
Managers needed fields for the quantities delivered on each outstanding PO line.

### What we did
Added `GoodsReceiptForm` that creates usable and damaged integer inputs for outstanding order lines, with an initial value of zero. Validation rejects negative quantities, excess quantities and an entirely empty delivery.

### Code
**CLASS — `purchasing/forms.py`**
```python
received_<line.pk>
damaged_<line.pk>
```

The actual field names are built dynamically from the order-line primary keys. After validation, the form assembles service-ready item dictionaries with product ID, usable quantity and damaged quantity.

### How we checked it
The form was exercised through the subsequent 13 interface tests; no independent form-only test output was supplied.

### Next
Connect the Manager-only receipt view.

## Step 365 — Connect the Manager-only receipt view

### Before this step
The receipt form had no HTTP workflow or redirect back to the purchase order.

### What we did
Added `goods_receipt_create` to `purchasing/views.py`. GET builds the form for an ordered or partially received PO; POST validates the fields, calls `receive_goods`, displays a message and redirects to the detail page.

### Code
**CLASS — `purchasing/views.py`**
```python
@login_required
def goods_receipt_create(request, pk):
    # Manager permission, eligible status and form validation
    # are enforced before calling receive_goods.
```

The view passes a generated submission key to the template. The service, not the template, is responsible for authoritative stock changes.

### How we checked it
The subsequent interface test suite passed all 13 tests.

### Next
Add receipt URL and pages.

## Step 366 — Add receipt URL and pages

### Before this step
The receipt view needed a named route and a discoverable action on the PO detail page.

### What we did
Added the `/purchase-orders/<int:pk>/receive/` route, a new receipt form template extending `base.html`, and Receive Goods / Receive Remaining Goods buttons on the detail page.

### Code
**CLASS — `purchasing/urls.py`**
```python
path(
    'purchase-orders/<int:pk>/receive/',
    views.goods_receipt_create,
    name='goods_receipt_create'
)
```

The receipt template uses `receipt_rows`, form errors, CSRF protection and a hidden `submission_key`. The detail page displays cumulative delivered quantities alongside ordered quantities.

### How we checked it
`python manage.py test purchasing.test_goods_receipt` passed five tests after the UI changes.

### Next
Test receipt routes and browser behavior.

## Step 367 — Test receipt routes and browser behavior

### Before this step
Service-level tests alone do not establish that users can submit the receipt form through Django views.

### What we did
Created `test_goods_receipt_views.py` with 13 tests covering authentication, Manager/Sales access, page rendering, redirects, partial and full deliveries, damaged quantities, invalid input, duplicate submissions and receiving the remainder.

### Code
**NEW — `purchasing/test_goods_receipt_views.py`**
```powershell
python manage.py test purchasing.test_goods_receipt_views
```

Django's test client checks the HTTP route and verifies database results after POST, not just the service return value.

### How we checked it
All 13 tests passed in 9.120 seconds with zero system-check issues.

### Next
Allow a partially received order to close short.

## Step 368 — Allow a partially received order to close short

### Before this step
An order might remain partly undelivered when the supplier will not send the balance. It should be possible to close it without pretending the remaining units arrived.

### What we did
Extended `change_purchase_order_status` to accept `closed_short` only for a `partially_received` order with outstanding quantities. No receipt or stock movement is created by closing.

### Code
**CLASS — `purchasing/services.py`**
```python
if new_status not in ('ordered', 'cancelled', 'closed_short'):
    raise ValueError('Invalid purchase order status.')

elif new_status == 'closed_short':
    if order.status != 'partially_received':
        raise ValueError(
            'Only partially received orders can be closed short.'
        )
```

Existing received quantities and receipt history remain intact. A closed-short order is not eligible for another receipt.

### How we checked it
Close-short tests later verified this behavior.

### Next
Expose Close Short on the purchase order detail page.

## Step 369 — Expose Close Short on the purchase order detail page

### Before this step
The status service could close an order, but a Manager still needed an action on the detail page.

### What we did
Added a confirmation-protected POST form with `status=closed_short`, shown only when the PO is partially received.

### Code
**CLASS — `purchasing/templates/purchasing/purchase_order_detail.html`**
```django
{% if order.status == 'partially_received' %}
<form method="post"
      action="{% url 'purchase_order_status' order.pk %}">
    {% csrf_token %}
    <input type="hidden" name="status" value="closed_short">
    <button type="submit">Close Short</button>
</form>
{% endif %}
```

The production template also includes an `onsubmit` confirmation. Visibility is a UI convenience; the service independently rejects invalid transitions.

### How we checked it
The full Close Short test suite subsequently passed.

### Next
Correct Close Short test assertions.

## Step 370 — Correct Close Short test assertions

### Before this step
Two tests failed because they searched for the text `Close Short` anywhere in the HTML. The supplier and product names also contained those words, even when the button was hidden.

### What we did
Changed button-visibility assertions to look for the form's hidden `status` input value, `value="closed_short"`, rather than incidental page text.

### Code
**CLASS — `purchasing/test_close_short.py`**
```python
self.assertContains(response, 'value="closed_short"')
self.assertNotContains(response, 'value="closed_short"')
```

This distinguishes a real Close Short action from a product name, status label or help paragraph. The original run had 11 passes and two false failures.

### How we checked it
The corrected tests were included in the later 87-test successful run.

### Next
Run full purchasing regression tests and inspect changed files.

## Step 371 — Run full purchasing regression tests and inspect changed files

### Before this step
The goods-receipt and Close Short features touch existing purchasing services, views and templates, so earlier workflows also needed regression coverage.

### What we did
Ran the full purchasing test suite and checked `git status --short` before publication. Git identified six modified and five new files, including the migration and three new test modules.

### Code
**TEST — purchasing module**
```powershell
python manage.py test purchasing

git status --short
```

The 87-test run verifies the purchasing module. The final status listing establishes which files need staging; it does not itself prove the browser was manually tested.

### How we checked it
87/87 passed in 213.331 seconds; Django system checks reported zero issues. Chapter 5 source changes were still local at this checkpoint.

### Next
Complete the chapter source commit and verify the published repository.

