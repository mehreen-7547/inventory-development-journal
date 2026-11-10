# MODULE 2 — Supplier and inventory foundations

## Chapter 8: Stock Movement and Stock Service (Journal Steps 275–279)

### Before this chapter
Chapter 7 could activate or deactivate products, but `Product.on_hand` was still a quantity with no real movement history. Module 0 contained temporary demonstration tables to prove conditional stock updates. We needed a real ledger and a single safe function for stock changes.

### What we built
The `inventory` Django app, a `StockMovement` ledger, the `change_stock()` service, a Manager-only per-product history page, and tests. Interactive development Steps **409–413** correspond to journal Steps **275–279**. The source was pushed to `main` at [`110c30f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/110c30f). The user ran **80 tests**, all passing. Chapter 9 will connect opening stock to the product creation form; this chapter does not yet provide that UI.

<a id="step-275"></a>
### Step 275: Create and register the inventory app (interactive Steps 409–410)

**Before this step:** Only `catalog` held products. No `inventory` app existed.

**What we did and why:** We ran `python manage.py startapp inventory`, creating the standard app skeleton. We registered it so Django could discover its models and migrations. Keeping stock ledger logic separate from product catalogue CRUD makes responsibilities clearer.

**Filename: `config/settings.py`**
```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "core",
    "accounts",
    "catalog",
    "inventory",
]
```
The new entry is `"inventory"`; the other entries already existed.

**Filename: `inventory/apps.py`** — Django generated this app configuration with `startapp`. The app also contains generated `__init__.py`, `admin.py`, `views.py`, `tests.py` and a `migrations/` directory; the generated tests file is replaced later.

**How we checked it:** `Get-ChildItem inventory` showed the generated files. The user confirmed Step 410 completed.

**Next:** Define the real movement ledger and stock-changing service.

<a id="step-276"></a>
### Step 276: Implement StockMovement, change_stock and service tests (interactive Step 411)

**Before this step:** `Product.on_hand` existed, but updating it alone would not explain who changed stock, why, or what the previous balance was.

**What we did and why:** Each movement records a signed quantity change and both balances. The service uses a conditional database update inside a transaction and creates the history row in the same transaction. A negative change is refused if there is not enough stock; a failed ledger insert rolls back the balance update. This is the only approved path for modifying `Product.on_hand`.

**Filename: `inventory/models.py`**
```python
class StockMovement(models.Model):
    MOVEMENT_CHOICES = (
        ('opening', 'Opening Stock'),
        ('sale', 'Sale'),
        ('void', 'Void Sale'),
        ('receipt', 'Goods Receipt'),
        ('adjustment', 'Stock Adjustment'),
    )

    product = models.ForeignKey('catalog.Product', on_delete=models.PROTECT)
    movement_type = models.CharField(max_length=20, choices=MOVEMENT_CHOICES)
    quantity_delta = models.IntegerField()
    balance_before = models.PositiveIntegerField()
    balance_after = models.PositiveIntegerField()
    reason = models.TextField()
    source = models.CharField(max_length=150, blank=True)
    created_by = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.PROTECT)
    created_at = models.DateTimeField(auto_now_add=True)
```
The model stores the event, its product, employee, time and reason. `PROTECT` prevents deleting related records that the ledger needs.

**Filename: `inventory/services.py`**
```python
def change_stock(product_id, delta, movement_type, reason, user, source=''):
    if type(delta) is not int or delta == 0:
        raise ValueError('Stock change must be a non-zero integer')
    if movement_type not in dict(StockMovement.MOVEMENT_CHOICES):
        raise ValueError('Invalid stock movement type')
    if not reason or not reason.strip():
        raise ValueError('Reason is required')

    with transaction.atomic():
        if delta < 0:
            changed = Product.objects.filter(
                pk=product_id, on_hand__gte=-delta
            ).update(on_hand=F('on_hand') + delta)
        else:
            changed = Product.objects.filter(
                pk=product_id
            ).update(on_hand=F('on_hand') + delta)

        if changed == 0:
            if not Product.objects.filter(pk=product_id).exists():
                raise ValueError('Product not found')
            raise ValueError('Not enough stock')

        product = Product.objects.get(pk=product_id)
        movement = StockMovement.objects.create(
            product=product,
            movement_type=movement_type,
            quantity_delta=delta,
            balance_before=product.on_hand - delta,
            balance_after=product.on_hand,
            reason=reason,
            source=source,
            created_by=user
        )
    return movement
```
`F('on_hand')` performs arithmetic in SQL; the negative case checks the balance in the same update. `transaction.atomic()` commits the balance and movement together or rolls both back.

**Filename: `inventory/migrations/0001_initial.py`** — Generated by `python manage.py makemigrations inventory`; creates the StockMovement database table. Do not hand-edit the generated migration.

**Filename: `inventory/tests.py`** — `StockServiceTests` contains seven checks: opening stock, stock reduction, insufficient stock, zero change, missing reason, invalid movement type and rollback on failed movement insertion.

**How we checked it:** Migration applied and system check passed. The first run had six passing tests and one error: the rollback test expected `ValueError`, while SQLite correctly raised `django.db.utils.IntegrityError` for a missing required employee. The production service did not need changing.

**Next:** Correct the test expectation.

<a id="step-277"></a>
### Step 277: Correct the rollback test (interactive Step 411 follow-up)

**Before this step:** The database correctly rejected a NULL `created_by_id`, but the test asserted the wrong exception class.

**What we did and why:** We changed only the test to expect `IntegrityError`, the exception raised for a database constraint failure. We still checked that stock remained zero and that no movement row was saved.

**Filename: `inventory/tests.py`**
```python
def test_failed_movement_rolls_back_stock(self):
    from django.db import IntegrityError

    with self.assertRaises(IntegrityError):
        change_stock(
            self.product.pk, 5, 'opening',
            'Test', None
        )

    self.product.refresh_from_db()
    self.assertEqual(self.product.on_hand, 0)
    self.assertEqual(StockMovement.objects.count(), 0)
```
The `with` block checks the database exception; the final two assertions verify the rollback.

**How we checked it:** The user reran `python manage.py test inventory`: **7 tests passed**.

**Next:** Show recorded movements to authorized Managers.

<a id="step-278"></a>
### Step 278: Add Manager-only product stock history (interactive Step 412)

**Before this step:** The ledger stored movements, but no page displayed them.

**What we did and why:** A Manager can open the history from an existing product's edit page. The view looks up the product, loads its movements newest first and renders a table. The history remains accessible even if the product is inactive. Sales and Admin roles cannot open this Manager-only page.

**Filename: `catalog/views.py`**
```python
from inventory.models import StockMovement

@login_required
@role_required('manager')
def product_stock_history(request, product_id):
    product = get_object_or_404(Product, pk=product_id)
    movements = StockMovement.objects.filter(
        product=product
    ).select_related('created_by').order_by('-created_at', '-pk')
    return render(request, 'catalog/product_stock_history.html', {
        'product': product,
        'movements': movements
    })
```
`get_object_or_404` rejects unknown product IDs. `select_related` retrieves the employee efficiently.

**Filename: `catalog/urls.py`**
```python
path(
    'products/<int:product_id>/history/',
    views.product_stock_history,
    name='product_stock_history'
),
```
The product ID selects the history to display.

**Filename: `catalog/templates/catalog/product_stock_history.html`** — New page extending `base.html`. It displays product SKU and current stock, followed by date, movement type, signed quantity, before/after balances, reason, source and employee. When there are no movements, it shows `No stock movements recorded yet.`.

**Filename: `catalog/templates/catalog/product_form.html`**
```html
<a href="{% url 'product_stock_history' form.instance.pk %}"
   class="btn btn-outline-primary mb-4">
    View Stock History
</a>
```
This link is inside the existing saved-product conditional, after the status form, so new unsaved products do not get a history link.

**Filename: `inventory/tests.py`** — `StockHistoryTests` adds seven tests: Manager access, empty history, Sales denial, Admin denial, anonymous redirect, history for inactive products and unknown product 404.

**How we checked it:** The user confirmed Step 412 completed. At final chapter verification, all project tests passed.

**Next:** Remove obsolete demonstration tests and publish.

<a id="step-279"></a>
### Step 279: Replace temporary tests, verify and publish (interactive Step 413)

**Before this step:** `core/tests.py` still contained two `TransactionTestCase` tests using temporary `ExampleProduct` tables from Module 0. They were no longer the correct way to test production stock.

**What we did and why:** We removed the two temporary stock tests and their unused imports. The original three Module 0 foundation checks stayed. Production stock behavior is now tested against `Product`, `StockMovement` and `change_stock()`.

**Filename: `core/tests.py`**
```python
from django.conf import settings
from django.db import connection
from django.test import TestCase
from django.urls import reverse

class ModuleZeroTests(TestCase):
    def test_home_page_redirects_anonymous_users(self):
        response = self.client.get(reverse("home"))
        self.assertRedirects(
            response,
            f'{reverse("login")}?next={reverse("home")}'
        )

    def test_local_timezone(self):
        self.assertEqual(settings.TIME_ZONE, "Asia/Kolkata")
        self.assertTrue(settings.USE_TZ)

    def test_sqlite_connection(self):
        self.assertEqual(connection.vendor, "sqlite")
        with connection.cursor() as cursor:
            cursor.execute("SELECT 1")
            self.assertEqual(cursor.fetchone()[0], 1)
```

**How we checked it:** The user ran `python manage.py test`: **80 tests**, all **OK**, with zero system-check issues. Git commit `110c30f` was created and pushed to `origin/main`. LF/CRLF warnings were non-blocking. [Source commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/110c30f).

**Next:** Module 2 Chapter 9 — initial opening stock on the Product Create form, using the existing stock service rather than changing `on_hand` directly.
