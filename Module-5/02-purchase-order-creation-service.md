# Module 5 — Chapter 2: Purchase Order Creation Service

**Source:** [commit 5160b69](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5160b691a8dafbc509f4b478c765fb003a2874a7) on `main`. **Interactive Steps:** 474–479. **Journal Steps:** 337–342.

The previous chapter created the database tables. This chapter adds a controlled Python service that validates an order before saving it. Creating a purchase order does **not** receive goods or change physical stock.

## Step 337 — Start the creation service (interactive 474)
**Before this step:** `PurchaseOrder` and `PurchaseOrderLine` models existed, but there was no service for safely creating both together.
**What we did:** Added `purchasing/services.py` with `create_purchase_order(po_number, supplier_id, items, user)`. It creates the order and loops through its lines within `transaction.atomic()`.
**Code — `purchasing/services.py` (source excerpt):**
```python
def create_purchase_order(po_number, supplier_id, items, user):
    # Validation runs before the following save operations.
    with transaction.atomic():
        # Look up the supplier, validate each product and save the order.
        ...
```
**Why:** A dedicated service centralizes business rules rather than duplicating them in forms and views. Atomicity avoids an order with only some of its lines saved.
**How we checked it:** This function is present in the final source commit; its initial version was strengthened in the next step.
**Next:** Validate inputs more rigorously.

## Step 338 — Strengthen service validation (interactive 475)
**Before this step:** The first implementation could accept malformed values and duplicate products.
**What we did:** Rejected unauthorized or inactive users, empty/oversized PO numbers, invalid supplier IDs, missing/inactive suppliers and products, supplier mismatches, duplicate products, invalid quantities and costs.
**Code — `purchasing/services.py` (source excerpt):**
```python
    if (
        not user
        or not user.is_authenticated
        or not user.is_active
        or user.role != 'manager'
    ):
        raise ValueError('Only active managers can create purchase orders.')

    if not isinstance(items, list) or not items:
        raise ValueError('At least one product is required.')

    if PurchaseOrder.objects.filter(po_number=po_number).exists():
        raise ValueError('Purchase order number already exists.')
```
**Walkthrough:** The user check limits access to active managers. The item check ensures there is something to order. The preliminary PO number lookup provides a friendly error; the database unique constraint remains the ultimate safeguard.
**Code — `purchasing/services.py` (item checks):**
```python
            if not isinstance(quantity, int) or isinstance(quantity, bool):
                raise ValueError('Quantity must be a whole number.')
            if quantity <= 0:
                raise ValueError('Quantity must be greater than zero.')
            if product_id in product_ids:
                raise ValueError('Duplicate product in purchase order.')
            product_ids.add(product_id)
```
**Walkthrough:** Boolean values are rejected even though Python treats `bool` as an `int` subclass. Duplicate product IDs are tracked in a set before any order is saved. The source also validates decimal cost precision, range and finiteness.
**How we checked it:** The focused service test suite was added next.
**Next:** Test valid and invalid scenarios.

## Step 339 — Test creation, validation and rollback (interactive 476)
**Before this step:** The new service lacked automated tests.
**What we did:** Added `PurchaseOrderServiceTests` in `purchasing/tests.py` with 16 cases for manager creation, stock preservation, roles, active records, supplier mismatches, duplicates, quantity/cost validation, missing products and atomic rollback.
**Code — `purchasing/tests.py` (source excerpt):**
```python
    def test_order_does_not_increase_stock(self):
        self.create_order()
        self.product.refresh_from_db()
        self.assertEqual(self.product.on_hand, 0)
```
**Why:** A purchase order is an intention to buy, not evidence that stock arrived.
**Code — `purchasing/tests.py` (rollback check excerpt):**
```python
        with patch.object(
            PurchaseOrderLine.objects,
            'create',
            side_effect=failing_create
        ):
            with self.assertRaises(RuntimeError):
                self.create_order(items=items)

        self.assertEqual(PurchaseOrder.objects.count(), 0)
        self.assertEqual(PurchaseOrderLine.objects.count(), 0)
```
**Walkthrough:** The mocked second-line failure forces the service to exit its transaction with an error. The database should contain neither the order nor any line.
**How we checked it:** User terminal reported `Ran 16 tests in 11.044s — OK`.
**Next:** Handle database-enforced uniqueness.

## Step 340 — Catch duplicate PO numbers from the database (interactive 477)
**Before this step:** A pre-insert `.exists()` check alone could not guarantee uniqueness under concurrent requests.
**What we did:** Added an inner atomic savepoint around order and line inserts and translated `IntegrityError` into a friendly `ValueError`. Added a test bypassing the preliminary duplicate lookup.
**Code — `purchasing/services.py` (source excerpt):**
```python
        try:
            with transaction.atomic():
                order = PurchaseOrder.objects.create(
                    po_number=po_number,
                    supplier=supplier,
                    created_by=user
                )
                for item in validated_items:
                    PurchaseOrderLine.objects.create(
                        purchase_order=order,
                        product=item['product'],
                        quantity_ordered=item['quantity'],
                        unit_cost=item['unit_cost']
                    )
        except IntegrityError:
            raise ValueError('Purchase order number already exists.')
        return order
```
**Walkthrough:** The inner `atomic()` creates a savepoint. If the database rejects a duplicate PO number, Django rolls back the inner block before the exception is handled. The exception handler currently treats *any* integrity error as a duplicate PO number; this may need refinement if other integrity constraints are introduced.
**Code — `purchasing/tests.py` (source excerpt):**
```python
    def test_database_duplicate_number_protection(self):
        self.create_order()
        with patch.object(PurchaseOrder.objects, 'filter') as mocked_filter:
            mocked_filter.return_value.exists.return_value = False
            with self.assertRaisesRegex(
                ValueError, 'Purchase order number already exists'
            ):
                self.create_order()
        self.assertEqual(PurchaseOrder.objects.count(), 1)
```
**How we checked it:** User reported all 17 focused service tests passing.
**Next:** Run the full suite.

## Step 341 — Verify Chapter 2 across the project (interactive 478)
**Before this step:** Focused tests had passed but regressions elsewhere were not yet checked.
**What we did:** Ran Django checks, migration drift check, and all tests.
**Code — terminal:**
```powershell
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
```
**How we checked it:** User terminal showed 0 system-check issues, `No changes detected`, and `Ran 221 tests in 209.321s — OK`.
**Next:** Publish source changes.

## Step 342 — Clean whitespace and push (interactive 479)
**Before this step:** The first commit attempt stopped because it removed trailing spaces but not blank lines at EOF.
**What we did:** Updated the PowerShell cleanup to remove both trailing whitespace on each line and extra EOF blank lines. Staged only `purchasing/services.py` and `purchasing/tests.py`, verified the staged diff, committed and pushed.
**Code — terminal (cleanup excerpt):**
```powershell
$content = [regex]::Replace(
    $content,
    '[^\S\r\n]+(?=\r?$)',
    '',
    [System.Text.RegularExpressions.RegexOptions]::Multiline
)
$content = $content.TrimEnd() + "`n"
```
**Code — terminal (Git commands):**
```powershell
git add -- purchasing/services.py purchasing/tests.py
git diff --cached --check
git commit -m "Complete Module 5 Chapter 2 purchase order creation service"
git push origin main
git status
```
**How we checked it:** User terminal confirmed commit `5160b69`, successful push, and clean working tree. GitHub independently confirms both changed files in the commit. LF/CRLF warnings were informational.
**Next:** Module 5 Chapter 3 — Purchase Order Creation Interface.

## Boundaries and cautions
- Purchase order creation is manager-only in the service; no dedicated form/view/URL is built yet.
- No stock is added when ordering. Goods receipt is a separate future workflow.
- `IntegrityError` handling is broad; it should eventually distinguish duplicate PO numbers from other integrity failures.
- SQLite does not offer the same row-level locking semantics as PostgreSQL/MySQL.
