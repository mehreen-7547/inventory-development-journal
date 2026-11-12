# Module 5 — Chapter 5: Goods Receipt and Close Short

**Verified source commit:** [`788a158`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/788a1580c1d7a8a9c6fb264eec605e50a2efcd6a) · **Steps:** 361–371 · **Purchasing tests:** 87 passed.

## Before we begin
Chapter 4 let Managers place and manage purchase orders, but did not record supplier deliveries. Creating a purchase order does not itself increase inventory. Chapter 5 begins when the supplier actually delivers goods.

## What we worked on
We built delivery records, safe stock updates, a receipt form and page, partial-delivery progress, Close Short and regression tests.

## Picture it this way
A shop orders 20 notebooks and starts with 10 in stock. A delivery contains 7 usable and 3 damaged notebooks. The order counts 10 delivered, but available stock increases by only 7. If the remaining 10 will never arrive, the Manager can close the order short.

## Chapter steps
- [Step 361: Store goods receipts and generate the migration](#step-361)
- [Step 362: Implement receive_goods as one transaction](#step-362)
- [Step 363: Test receipt quantities and idempotency](#step-363)
- [Step 364: Build a dynamic GoodsReceiptForm](#step-364)
- [Step 365: Connect the Manager-only receipt view](#step-365)
- [Step 366: Register the receipt route and form page](#step-366)
- [Step 367: Show received totals and receipt actions](#step-367)
- [Step 368: Allow partially received orders to close short](#step-368)
- [Step 369: Expose the Close Short action in the template](#step-369)
- [Step 370: Test Close Short and repair two brittle assertions](#step-370)
- [Step 371: Run regression tests and publish the source](#step-371)

## Step 361 — Store goods receipts and generate the migration

### Before this step
We could create purchase orders, but the database did not have a record for individual deliveries. We could not distinguish damaged goods from usable deliveries, or remember the receiving employee. A database change was needed before a receipt could be processed.

### What we did and why
We added a cumulative received quantity on purchase-order lines, a Closed Short status and two new models for receipts and receipt lines. Django generated migration 0002 to add the new field and tables.

### Code
Open `purchasing/models.py` and find these lines in the change made for this step:

```python


class GoodsReceipt(models.Model):
    receipt_number = models.CharField(
        max_length=20,
        unique=True
    )
    purchase_order = models.ForeignKey(
        PurchaseOrder,
        on_delete=models.PROTECT,
        null=True,
        blank=True
    )
    received_by = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.PROTECT
    )
    received_at = models.DateTimeField(auto_now_add=True)
    note = models.TextField(blank=True)
    submission_key = models.CharField(
        max_length=100,
        unique=True
    )

    def __str__(self):
        return self.receipt_number
```

Open `purchasing/migrations/0002_purchaseorderline_quantity_received_and_more.py` and inspect these added lines for the same step:

```python

    operations = [
        migrations.AddField(
            model_name='purchaseorderline',
            name='quantity_received',
            field=models.PositiveIntegerField(default=0),
        ),
        migrations.AlterField(
            model_name='purchaseorder',
            name='status',
            field=models.CharField(choices=[('draft', 'Draft'), ('ordered', 'Ordered'), ('partially_received', 'Partially Received'), ('received', 'Received'), ('cancelled', 'Cancelled'), ('closed_short', 'Closed Short')], default='draft', max_length=20),
        ),
        migrations.CreateModel(
            name='GoodsReceipt',
            fields=[
                ('id', models.BigAutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('receipt_number', models.CharField(max_length=20, unique=True)),
                ('received_at', models.DateTimeField(auto_now_add=True)),
                ('note', models.TextField(blank=True)),
                ('submission_key', models.CharField(max_length=100, unique=True)),
                ('purchase_order', models.ForeignKey(blank=True, null=True, on_delete=django.db.models.deletion.PROTECT, to='purchasing.purchaseorder')),
                ('received_by', models.ForeignKey(on_delete=django.db.models.deletion.PROTECT, to=settings.AUTH_USER_MODEL)),
```

### Code walkthrough
`PurchaseOrderLine.quantity_received` begins at zero and counts every delivered unit, including damaged units. A `GoodsReceipt` is the header for one delivery and identifies the purchase order, receiver, date and unique submission key. Its `GoodsReceiptLine` children identify products and separate usable from damaged quantities. The migration makes these Python declarations real database structures; it also updates the list of allowed purchase-order statuses. We use protected foreign keys to retain delivery history. This matters because deleting a product or order should not silently destroy evidence of received goods.

### How we checked it
Migration generation and application succeeded. Django system checks passed and the purchasing suite later passed 87 tests.

### Where to go next
Previous: [Chapter 4](05-purchase-order-management-interface.md). Next: [Step 362](#step-362).

## Step 362 — Implement receive_goods as one transaction

### Before this step
Saving a receipt must also update the order and available stock. If any one of those writes fails, the database must not retain half a delivery. The service must also refuse unauthorized users and deliveries above the outstanding quantity.

### What we did and why
We created a Manager-only `receive_goods` service with validation, a unique submission key and an atomic transaction. It checks each item, writes receipt records, updates cumulative received quantities and adds only usable units through the inventory service.

### Code
Open `purchasing/services.py` and find these lines in the change made for this step:

```python


def receive_goods(order_id, items, submission_key, user):
    if (
        not user
        or not user.is_authenticated
        or not user.is_active
        or user.role != 'manager'
    ):
        raise ValueError('Only active managers can receive goods.')

    if not isinstance(submission_key, str) or not submission_key.strip():
        raise ValueError('Submission key is required.')

    submission_key = submission_key.strip()

    if len(submission_key) > 100:
        raise ValueError('Invalid submission key.')

    if not isinstance(items, list) or not items:
        raise ValueError('At least one product is required.')

    with transaction.atomic():
        existing = GoodsReceipt.objects.filter(
            submission_key=submission_key
        ).first()

        if existing:
            if existing.purchase_order_id != order_id:
                raise ValueError('Submission key already used.')
```

### Code walkthrough
The function checks the user role and activity before touching the database. It rejects empty or excessively long submission keys and empty item lists. Inside `transaction.atomic()`, an existing receipt with the same key is returned rather than processed twice. The order must be ordered or partially received. Each product must belong to that order, appear only once in the request, and have nonnegative usable and damaged quantities whose sum does not exceed the remainder. The service then saves the receipt, calls `change_stock` only for usable goods and updates order status. The committed code uses `select_for_update()` even though the SQLite guidance discourages it; this is documented as a known deviation.

### How we checked it
Five receipt service tests passed; the full purchasing suite later passed.

### Where to go next
Previous: [Step 361](#step-361). Next: [Step 363](#step-363).

## Step 363 — Test receipt quantities and idempotency

### Before this step
A service can return successfully while producing the wrong stock total. We needed repeatable examples with known opening stock and purchase quantities. We also needed to catch a repeated submission that might otherwise add the same delivery twice.

### What we did and why
We added five service tests for partial delivery, damaged goods, complete delivery, over-receipt and duplicate submission. The tests use a real test database and refresh objects before comparing results.

### Code
Open `purchasing/test_goods_receipt.py` and find these lines in the change made for this step:

```python
        )

    def test_partial_receipt_updates_stock(self):
        receive_goods(
            self.order.pk,
            [{
                'product_id': self.product.pk,
                'quantity_received': 12,
                'quantity_damaged': 0
            }],
            'receipt-key-001',
            self.manager
        )

        self.product.refresh_from_db()
        self.order.refresh_from_db()

        self.assertEqual(self.product.on_hand, 22)
        self.assertEqual(self.order.status, 'partially_received')

    def test_damaged_goods_do_not_increase_stock(self):
        receive_goods(
            self.order.pk,
            [{
                'product_id': self.product.pk,
                'quantity_received': 7,
```

### Code walkthrough
The setup creates a Manager, supplier and product, gives the product ten units of opening stock and orders twenty more. Receiving twelve usable units must produce stock of twenty-two and status `partially_received`. Receiving seven usable and three damaged must produce stock of seventeen, because damaged goods do not become available stock. Receiving all twenty finishes the order. Attempting twenty-one raises an error and leaves stock unchanged. Repeating the same submission key must leave one receipt and one stock movement, proving that the same delivery is not applied twice.

### How we checked it
`python manage.py test purchasing.test_goods_receipt` passed all five tests.

### Where to go next
Previous: [Step 362](#step-362). Next: [Step 364](#step-364).

## Step 364 — Build a dynamic GoodsReceiptForm

### Before this step
Different purchase orders contain different products and quantities. A fixed HTML form cannot know which items still need delivery. We needed one usable and one damaged input for each outstanding order line.

### What we did and why
We created a form that reads the selected purchase order, calculates each remaining quantity and builds named integer fields dynamically. The form also rejects empty deliveries and combined quantities above the remaining balance.

### Code
Open `purchasing/forms.py` and find these lines in the change made for this step:

```python


class GoodsReceiptForm(forms.Form):

    def __init__(self, *args, purchase_order=None, **kwargs):
        super().__init__(*args, **kwargs)

        self.purchase_order = purchase_order
        self.order_lines = []

        if purchase_order is None:
            return

        lines = PurchaseOrderLine.objects.filter(
            purchase_order=purchase_order
        ).select_related('product').order_by('pk')

        for line in lines:
            remaining = (
                line.quantity_ordered - line.quantity_received
            )

            if remaining <= 0:
                continue

            self.order_lines.append(line)
```

### Code walkthrough
The constructor accepts `purchase_order`, queries its lines and skips those already complete. Each remaining line receives `received_<line.pk>` and `damaged_<line.pk>` fields, so values from different products cannot be confused. Both fields start at zero and reject negative numbers. Their individual maximum is the remaining quantity. The `clean()` method adds usable and damaged quantities together because each field could pass separately while their sum exceeds the order. It then builds a list of dictionaries containing product ID and quantities. The service receives this normalized list instead of reading raw browser fields.

### How we checked it
Thirteen receipt-view tests later exercised this form through HTTP submissions; all passed.

### Where to go next
Previous: [Step 363](#step-363). Next: [Step 365](#step-365).

## Step 365 — Connect the Manager-only receipt view

### Before this step
The form could validate quantities but was not yet connected to a browser page. Managers needed a GET page to see the form and a POST action to submit it. The view had to reject non-Managers and orders that were already finished.

### What we did and why
We added `goods_receipt_create` with authentication and Manager checks, eligible-order checks, form binding, a service call, success messages and a redirect. It also creates the table rows and submission key for the template.

### Code
Open `purchasing/views.py` and find these lines in the change made for this step:

```python

@login_required
def goods_receipt_create(request, pk):
    if not manager_allowed(request.user):
        messages.error(
            request,
            'Only active managers can receive goods.'
        )
        return redirect('home')

    order = get_object_or_404(
        PurchaseOrder.objects.select_related('supplier'),
        pk=pk
    )

    if order.status not in ('ordered', 'partially_received'):
        messages.error(
            request,
            'This purchase order cannot receive goods.'
        )
        return redirect('purchase_order_detail', pk=pk)

    if request.method == 'POST':
        form = GoodsReceiptForm(
            request.POST,
            purchase_order=order
```

### Code walkthrough
The `login_required` decorator requires a signed-in user; `manager_allowed` applies the specific role restriction. The order is loaded with its supplier. Only `ordered` and `partially_received` orders can open the form. GET creates an unbound form and a new UUID-based submission key. POST binds submitted fields and calls `receive_goods` after form validation. A service `ValueError` becomes a visible form error rather than a server crash. A successful receipt displays its number and returns to the purchase-order detail. The view prepares `receipt_rows` so the template can show ordered, received and remaining quantities together.

### How we checked it
The view test suite passed 13 tests for permissions, display, receipt submissions and invalid data.

### Where to go next
Previous: [Step 364](#step-364). Next: [Step 366](#step-366).

## Step 366 — Register the receipt route and form page

### Before this step
A Django view cannot be opened until it has a URL. Managers also needed an HTML page showing the correct product names and quantities. A simple form with only quantity boxes would make mistakes more likely.

### What we did and why
We registered a named receipt URL and created the receipt template. It extends the existing layout, shows order context and renders usable and damaged inputs beside each outstanding product.

### Code
Open `purchasing/urls.py` and find these lines in the change made for this step:

```python
    path(
        'purchase-orders/<int:pk>/receive/',
        views.goods_receipt_create,
        name='goods_receipt_create'
    ),
```

Open `purchasing/templates/purchasing/goods_receipt_form.html` and inspect these added lines for the same step:

```django

{% extends 'base.html' %}

{% block content %}

<h2>Receive Goods</h2>

<p>
    Purchase Order: {{ order.po_number }}
</p>

<p>
    Supplier: {{ order.supplier.name }}
</p>

<p>
    Status: {{ order.get_status_display }}
</p>

<form method="post">
    {% csrf_token %}
```

### Code walkthrough
The URL captures the purchase-order primary key and sends it to `goods_receipt_create`. The route name lets Django generate links without hard-coding the address. The new template extends `base.html` to retain the application's common layout. It displays the order number, supplier and status so the Manager can verify the intended purchase order. A CSRF token protects the POST form and a hidden submission key identifies the delivery attempt. The table shows ordered, previously received and remaining quantities. Field and non-field errors explain invalid input, while Cancel returns without recording anything.

### How we checked it
The Manager form-rendering and unauthorized-access tests passed.

### Where to go next
Previous: [Step 365](#step-365). Next: [Step 367](#step-367).

## Step 367 — Show received totals and receipt actions

### Before this step
After a partial delivery, the detail page still needed to show how much had arrived. Managers also needed a direct way to receive the remaining goods. The existing table and actions did not explain this new workflow.

### What we did and why
We added a Received column, adjusted the total row alignment and placed a Receive Goods action on eligible orders. The action changes to Receive Remaining Goods after a partial receipt.

### Code
Open `purchasing/templates/purchasing/purchase_order_detail.html` and find these lines in the change made for this step:

```django
<th class="text-end">Received</th>
<td class="text-end">
    {{ item.line.quantity_received }}
</td>
<th colspan="5" class="text-end">
    Order Total
</th>

            {% if order.status == 'ordered' or order.status == 'partially_received' %}
            <a href="{% url 'goods_receipt_create' order.pk %}"
               class="btn btn-success">
```

### Code walkthrough
The new table cell reads `item.line.quantity_received`, the cumulative amount across all deliveries for that product. It is not the quantity from only the most recent receipt. The table footer spans one extra column so the order total remains aligned. The receipt link appears only for `ordered` and `partially_received` states, matching the service's eligibility rule. The text changes after a partial receipt so the next action is clear. This visibility check helps the user but is not the security check; the view and service independently reject an ineligible request.

### How we checked it
The receipt-view tests checked the order detail and receipt workflow. The purchasing suite previously passed 87 tests; the full-project test count was not captured in the pasted terminal output.

### Where to go next
Previous: [Step 366](#step-366). Next: [Step 368](#step-368).

## Step 368 — Allow partially received orders to close short

### Before this step
A supplier may confirm that the outstanding quantity will never arrive. Keeping the order partly received indefinitely is misleading, but marking it received would invent a delivery. We needed a final state that preserves the actual receipt history.

### What we did and why
We extended `change_purchase_order_status` with `closed_short`, restricted to orders already partially received and still having outstanding quantities. This status change does not touch stock or remove earlier receipts.

### Code
Open `purchasing/services.py` and find these lines in the change made for this step:

```python
    if new_status not in ('ordered', 'cancelled', 'closed_short'):
        raise ValueError('Invalid purchase order status.')

    with transaction.atomic():
        try:
            order = PurchaseOrder.objects.select_for_update().get(
                pk=order_id
            )
        except PurchaseOrder.DoesNotExist:
            raise ValueError('Purchase order not found.')

        if new_status == 'ordered':
            if order.status != 'draft':
                raise ValueError(
                    'Only draft purchase orders can be marked as ordered.'
                )

        elif new_status == 'cancelled':
            if order.status not in ('draft', 'ordered'):
                raise ValueError(
                    'This purchase order cannot be cancelled.'
                )

        elif new_status == 'closed_short':
            if order.status != 'partially_received':
                raise ValueError(
                    'Only partially received orders can be closed short.'
                )

            lines = PurchaseOrderLine.objects.select_for_update().filter(
                purchase_order=order
            )

            if not any(
                line.quantity_received < line.quantity_ordered
                for line in lines
            ):
                raise ValueError(
                    'This purchase order has no outstanding quantity.'
                )

        order.status = new_status
        order.save(update_fields=['status'])

        return order
```

### Code walkthrough
The service now accepts `closed_short` as a possible requested status, but it still checks whether the transition is valid. The new branch requires the current state to be `partially_received`. It then looks at the order lines and requires at least one received quantity to be lower than its ordered quantity. An already complete order cannot close short. When valid, the service updates only the purchase-order status inside its transaction. It does not call the stock service, so the on-hand balance remains exactly what the real deliveries produced. The receipt records and line totals remain intact.

### How we checked it
Close Short tests verified allowed and rejected transitions, preserved stock and preserved receipt records.

### Where to go next
Previous: [Step 367](#step-367). Next: [Step 369](#step-369).

## Step 369 — Expose the Close Short action in the template

### Before this step
The service rule existed, but the Manager had no normal page action for it. A partially received order needed a visible Close Short button. The action should also warn the Manager that the remaining goods will not be received against this order.

### What we did and why
We added a POST form with a hidden `closed_short` value and a confirmation prompt. It appears only on partially received orders. Existing cancellation remains limited to draft and ordered states.

### Code
Open `purchasing/templates/purchasing/purchase_order_detail.html` and find these lines in the change made for this step:

```django
            {% if order.status == 'partially_received' %}
                    Receive Remaining Goods
                {% else %}
                    Receive Goods
                {% endif %}
            </a>
            {% endif %}

            {% if order.status == 'partially_received' %}
            <form method="post"
                  action="{% url 'purchase_order_status' order.pk %}"
                  onsubmit="return confirm('Close this purchase order without receiving the remaining goods? This action cannot be undone.');">
                {% csrf_token %}
                <input type="hidden" name="status" value="closed_short">

                <button type="submit" class="btn btn-outline-warning">
                    Close Short
                </button>
            </form>
            {% endif %}
```

### Code walkthrough
The template condition checks `order.status == 'partially_received'` before rendering the action. The form posts to the existing purchase-order status endpoint, so no separate status view is required. Its CSRF token protects the submission, and the hidden input provides the exact status value the service understands. The browser confirmation warns that the action cannot be undone through the normal interface. The button is deliberately absent for draft, ordered, received and closed-short orders. Even if someone constructs a manual POST, the service still checks the Manager's role and the order's remaining quantity.

### How we checked it
The Close Short interface tests covered button visibility and the POST transition.

### Where to go next
Previous: [Step 368](#step-368). Next: [Step 370](#step-370).

## Step 370 — Test Close Short and repair two brittle assertions

### Before this step
Changing an order's final state could accidentally alter stock or allow later receipts. We added regression tests for those risks. The first run also exposed two faulty HTML assertions, not a failure of the Close Short business rule.

### What we did and why
We added thirteen Close Short tests and corrected assertions that searched for the phrase Close Short anywhere on the page. The fixture's supplier and product names contained that phrase, causing false failures. The revised assertions search for the actual hidden form input.

### Code
Open `purchasing/test_close_short.py` and find these lines in the change made for this step:

```python
        self.assertContains(
            response,
            'value="closed_short"'
        )

    def test_close_short_button_hidden_for_ordered_order(self):
        self.client.force_login(self.manager)

        response = self.client.get(self.detail_url)

        self.assertNotContains(
            response,
            'value="closed_short"'
        )

    def test_close_short_button_hidden_after_closing(self):
        self.receive_partial_delivery()

        change_purchase_order_status(
            self.order.pk,
            'closed_short',
            self.manager
        )

        self.client.force_login(self.manager)
```

### Code walkthrough
The tests create an ordered purchase order and helper method for a partial delivery. They check that a Manager can close it, Sales cannot, stock remains unchanged, and previous receipts still exist. They also verify that a closed-short order cannot receive more goods. For the HTML checks, the original text assertion was too broad: the page could legitimately mention Close Short in a product name while hiding the action. Checking `value="closed_short"` instead targets the form control itself. This makes the test about button availability rather than incidental page text.

### How we checked it
The first run passed 11 of 13 tests; two false failures were fixed. The later 87-test purchasing run passed.

### Where to go next
Previous: [Step 369](#step-369). Next: [Step 371](#step-371).

## Step 371 — Run regression tests and publish the source

### Before this step
Receipt processing changes several files, so passing one small test is not enough. We needed to confirm the browser workflow and existing purchasing behavior together. We also needed a precise source commit for the learning journal.

### What we did and why
We completed thirteen receipt-view tests, ran the purchasing suite and inspected the changed-file list. The guarded source-push block then committed the eleven Chapter 5 files. GitHub confirms the source commit and its exact file list.

### Code
Open `purchasing/test_goods_receipt_views.py` and find these lines in the change made for this step:

```python
        )

    def test_partial_receipt_updates_stock_and_status(self):
        self.client.force_login(self.manager)

        response = self.client.post(
            self.receipt_url,
            self.receipt_data(12, 0, 'view-receipt-001')
        )

        self.assertRedirects(
            response,
            self.detail_url
        )

        self.product.refresh_from_db()
        self.order.refresh_from_db()

        line = self.order.lines.get()

        self.assertEqual(self.product.on_hand, 22)
        self.assertEqual(line.quantity_received, 12)
        self.assertEqual(
            self.order.status,
            'partially_received'
        )
```

### Code walkthrough
The view tests log in a Manager, post form values, follow the expected redirect and inspect persisted stock, receipt and order-line data. Other tests reject unauthorized users, over-receipt and repeated submissions. The final `python manage.py test purchasing` run discovered 87 tests and reported zero failures or system-check issues. The source commit includes six modified and five new purchasing files. The closing PowerShell block also invoked the full-project test command, but its terminal output was not provided, so no full-project count is asserted here. GitHub confirms the new commit and shows no `.env` or database file among its changes.

### How we checked it
87 purchasing tests passed in 213.331 seconds, zero system-check issues. GitHub verified commit `788a158` with 11 purchasing files; the full-project terminal result was not supplied.

### Where to go next
Previous: [Step 370](#step-370). [Return to the chapter opening](#before-we-begin).

## Chapter summary
We added receipt models and a migration, built and tested atomic receipt processing, connected the form and page, displayed delivered quantities, implemented Close Short and repaired two brittle test assertions. Only usable goods increase available stock. The source commit contains eleven purchasing files, and all 87 purchasing tests passed.

**Recall question:** If an order for 20 units receives 7 usable and 3 damaged, what happens to the order-line received total and the product's available stock?
