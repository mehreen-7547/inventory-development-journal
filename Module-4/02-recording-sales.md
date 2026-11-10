# Module 4 — Chapter 2: Recording Sales

**Journal Steps 307–316 · Interactive Steps 445–457**  
**Verified source:** [commit `b2ae5c6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/b2ae5c6355e98e3ad9c0a5206aec2ba3be9a6418) on `main`. User terminal: **27 Sales tests, 138 total tests passed**, no pending migrations; GitHub confirms the final source commit.  
**Scope:** `record_sale()`, validation, discounts, sale lines, stock ledger updates, submission-key checks and rollback tests. **No sales forms, views, checkout page, void service or dashboard in this chapter.**

## Before this chapter

Chapter 1 established the Sale and SaleLine models but did not record transactions. We now need a service that writes a sale and subtracts stock together, rejects unsafe requests and can be tested without a web page. The source commit contains the final implementation; code excerpts below are taken from it. Interactive steps were sometimes combined in the journal because they modified the same behavior.

<a id="step-307"></a>
## Step 307 — Create the record_sale service (445–447)

### Before this step
The Sales models existed, but no Python function could turn a list of purchased products into a transaction.

### What we did and why
We created a dedicated service rather than putting business logic into a view. The service takes sale items, the acting user, a submission key and optional discount settings.

**Filename: `sales/services.py`** — source excerpt:
```python
from decimal import Decimal, InvalidOperation
from uuid import uuid4
from django.db import IntegrityError, transaction
from catalog.models import Product
from inventory.services import change_stock
from .models import Sale, SaleLine


def record_sale(items, user, submission_key, discount_type='none', discount_value=0):
```

### Code walkthrough
`Decimal` avoids floating-point currency arithmetic; `uuid4` helps generate sale numbers; `transaction` makes a sale all-or-nothing. `change_stock` reuses the existing stock ledger rather than editing product stock directly.

### How we checked it
The new file and callable are present in the published source. Initial service tests were added in the same chapter.

### Next
Validate the caller and inputs.

<a id="step-308"></a>
## Step 308 — Validate user, key and discount (447–449)

### Before this step
A service can be called by a view or other Python code; a hidden button is not a security check.

### What we did and why
We required an authenticated active sales/manager user, a nonempty submission key, a valid discount type and a finite nonnegative discount with at most two decimal places.

**Filename: `sales/services.py`** — source excerpt:
```python
if not user or not user.is_authenticated or not user.is_active:
    raise ValueError('An active user is required')

if user.role not in ['sales', 'manager']:
    raise ValueError('You cannot record sales')

if not isinstance(submission_key, str) or not submission_key.strip():
    raise ValueError('Submission key is required')

if discount_type not in ['none', 'fixed', 'percent']:
    raise ValueError('Invalid discount type')
```

### Code walkthrough
The role test is enforced in the service, not merely in the page. `Decimal(str(discount_value))` converts input; `is_finite()` rejects NaN/infinity. Percentage cannot exceed 100; `none` requires zero.

### How we checked it
Tests reject admin/inactive users, negative or excessive percentage discounts, and invalid keys/precision.

### Next
Make repeat submissions safe.

<a id="step-309"></a>
## Step 309 — Return an existing matching sale (450)

### Before this step
A user might press Submit twice, and a retry must not deduct inventory twice.

### What we did and why
We first look up the submission key. A retry by the same user with identical products, quantities and discount returns the saved Sale; a changed payload or another user is rejected.

**Filename: `sales/services.py`** — source excerpt:
```python
existing_sale = Sale.objects.filter(submission_key=submission_key).first()

if existing_sale:
    if existing_sale.created_by_id != user.pk:
        raise ValueError('Submission key already used')

    original_items = {
        (line.product_id, line.quantity)
        for line in existing_sale.lines.all()
    }
```

### Code walkthrough
The existing record is the authoritative result of the earlier request. Comparing product/quantity pairs and discount fields prevents silently treating a different order as the same sale.

### How we checked it
Tests confirm the same key does not reduce stock twice and mismatched keys are rejected.

### Next
Prepare new sale lines.

<a id="step-310"></a>
## Step 310 — Validate products and calculate totals (447–452)

### Before this step
A new sale must use real active products and sensible quantities before changing stock.

### What we did and why
We reject duplicate product IDs, nonexistent/inactive products and nonpositive or noninteger quantities. Each line uses the product's current unit price, and the subtotal is the sum of line totals.

**Filename: `sales/services.py`** — source excerpt:
```python
line_total = product.unit_price * quantity

prepared_items.append({
    'product': product,
    'quantity': quantity,
    'unit_price': product.unit_price,
    'line_total': line_total,
})

subtotal += line_total
```

### Code walkthrough
The prepared dictionary keeps all details needed for the eventual SaleLine. Fixed discounts subtract a currency amount; percentage discounts calculate a proportion rounded to cents. A discount larger than subtotal is refused.

### How we checked it
Tests cover fixed/percentage discounts, invalid products, duplicate products, invalid quantities and excessive discounts.

### Next
Write the sale and stock movements together.

<a id="step-311"></a>
## Step 311 — Save Sale, SaleLine and stock movement atomically (447–452)

### Before this step
A partial sale would be dangerous: stock could fall without a matching sale record, or vice versa.

### What we did and why
Inside `transaction.atomic()` we create one Sale, one SaleLine per item and a matching stock movement using the existing inventory service.

**Filename: `sales/services.py`** — source excerpt:
```python
SaleLine.objects.create(
    sale=sale,
    product=product,
    product_name=product.name,
    quantity=quantity,
    unit_price=item['unit_price'],
    line_total=item['line_total']
)

change_stock(
    product_id=product.pk,
    delta=-quantity,
    movement_type='sale',
    reason='Sale recorded',
    user=user,
    source=sale.sale_number
)
```

### Code walkthrough
`product_name` and `unit_price` are historical snapshots. Negative `delta` subtracts stock; `source` links the ledger entry to the sale number. The outer atomic block rolls everything back if one operation fails.

### How we checked it
Tests confirm a normal sale reduces stock, creates line items and creates corresponding ledger entries.

### Next
Check multi-product behavior.

<a id="step-312"></a>
## Step 312 — Test multiple products and rollback (451–452)

### Before this step
One sale can contain several products; a failure in any one must cancel the entire transaction.

### What we did and why
We tested two successful product lines and a failed order where the second product did not have enough stock.

**Filename: `sales/tests.py`** — source excerpt:
```python
self.assertEqual(sale.lines.count(), 2)
self.assertEqual(sale.subtotal, Decimal('190.00'))
self.assertEqual(sale.total, Decimal('190.00'))
```

### Code walkthrough
The successful test checks totals, both product balances and two sale ledger movements. The failure test checks that no Sale, SaleLine or sale movement remains.

### How we checked it
Both tests are present in the published source and included in 27 passing Sales tests.

### Next
Handle a duplicate key enforced by the database.

<a id="step-313"></a>
## Step 313 — Handle database uniqueness errors (453)

### Before this step
Application-level lookup alone cannot prevent two concurrent requests from trying the same key.

### What we did and why
We wrapped the Sale insert in a nested atomic savepoint and convert `IntegrityError` to a controlled `ValueError`. The Sale model's unique submission key is the final database safeguard.

**Filename: `sales/services.py`** — source excerpt:
```python
try:
    with transaction.atomic():
        sale = Sale.objects.create(
            sale_number='S' + uuid4().hex[:16].upper(),
            submission_key=submission_key,
            created_by=user,
            discount_type=discount_type,
            discount_value=discount_value,
            subtotal=subtotal,
            total=total
        )
except IntegrityError:
    raise ValueError(
        'Sale could not be created. '
        'Please check whether it was already submitted.'
    )
```

### Code walkthrough
The inner atomic block creates a savepoint so a failed insert can be handled without leaving Django's transaction in a broken state. A racing retry may receive a controlled error rather than the original sale; this is not a concurrency stress test.

### How we checked it
The code is published and the complete Sales suite passed.

### Next
Verify the uniqueness constraint.

<a id="step-314"></a>
## Step 314 — Test database duplicate-key protection (454)

### Before this step
We needed to check that the database itself rejects a second Sale with an existing submission key.

### What we did and why
We attempted a second direct `Sale.objects.create()` using the first sale's key and asserted that the database raises `IntegrityError`.

**Filename: `sales/tests.py`** — source excerpt:
```python
with self.assertRaises(IntegrityError):
    with transaction.atomic():
        Sale.objects.create(
            sale_number='S' + uuid4().hex[:16].upper(),
            submission_key=sale.submission_key,
            created_by=self.user
        )
```

### Code walkthrough
This test bypasses `record_sale` on purpose: it proves the unique database field works even when application checks are skipped. The nested transaction isolates the expected failure.

### How we checked it
The user reported 26 Sales tests passing after this test.

### Next
Prove a late failure rolls back earlier stock deductions.

<a id="step-315"></a>
## Step 315 — Test rollback after the first deduction (455)

### Before this step
A failure after one successful stock movement is a stronger test than an invalid request rejected before any write.

### What we did and why
We patched `sales.services.change_stock` to fail on its second call. We then verified the first deduction, Sale and SaleLines were all rolled back.

**Filename: `sales/tests.py`** — source excerpt:
```python
with patch(
    'sales.services.change_stock',
    side_effect=failing_change_stock
):
    with self.assertRaises(ValueError):
        self.make_sale([
            {'product_id': self.product.pk, 'quantity': 3},
            {'product_id': self.second_product.pk, 'quantity': 2}
        ])
```

### Code walkthrough
`patch` substitutes a controlled failure only during the test. The first call uses the real stock service; the second raises an exception. `refresh_from_db()` confirms both products still have their original stock.

### How we checked it
User terminal: 27 Sales tests and 138 project tests passed, with no pending migrations.

### Next
Clean formatting and publish.

<a id="step-316"></a>
## Step 316 — Verify, repair whitespace and publish (456–457)

### Before this step
The behavior passed tests, but Git's whitespace checks found trailing spaces in two files.

### What we did and why
We removed trailing whitespace, staged only the service and tests, reran the Sales tests and pushed the source commit.

**Filename: `terminal (PowerShell)`** — commands:
```powershell
git add sales/services.py sales/tests.py
git diff --cached --check
python manage.py test sales
git commit -m "Complete Module 4 Chapter 2 sales transaction service"
git push origin main
```

### Code walkthrough
`git diff --cached --check` checks exactly what will be committed. The initial commands stopped on whitespace; the final push succeeded. LF/CRLF warnings on Windows were not the cause of failure.

### How we checked it
GitHub confirms source commit `b2ae5c6`; user reported all final checks successful. The earlier complete run showed 138 passing tests.

### Next
Next chapter: build the Sales interface; voiding is not implemented yet.

