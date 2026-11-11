# Module 5 — Chapter 1: Purchase Order Setup

**Source:** [commit 45bd7a2](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/45bd7a265a4b0eb8a3eac846582dede8d027b42f) on `main`. **Interactive Steps:** 469–473. **Journal Steps:** 332–336.

This chapter introduces purchasing as a separate Django app. A purchase order records an intention to buy products from a supplier; creating one must **not** increase stock. Goods receipt and stock updates are future work.

## Step 332 — Create the Purchasing app (interactive 469)
**Before this step:** Sales existed, but no Purchasing Django app existed.
**What we did:** Ran `python manage.py startapp purchasing` if the directory was absent. Django generated `purchasing/apps.py`, `purchasing/models.py`, `purchasing/admin.py`, `purchasing/tests.py`, `purchasing/views.py`, and `purchasing/migrations/__init__.py`.
**Code — terminal:**
```powershell
if (-not (Test-Path "purchasing")) {
    python manage.py startapp purchasing
} else {
    Write-Host "Purchasing app already exists — skipped"
}
```
**How we checked it:** The source commit contains the generated app files.
**Next:** Register the app.

## Step 333 — Register Purchasing (interactive 470)
**Before this step:** Django had not loaded the new app.
**What we did:** Added Purchasing to Django's installed apps, preserving all existing entries.
**Code — `config/settings.py` (excerpt):**
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
    "sales",
    "purchasing",
]
```
**Why:** Django discovers the app's models, migrations, admin registration and tests through app registration.
**How we checked it:** The commit includes this change; user reported Django checks passing.
**Next:** Define the schema.

## Step 334 — Define purchase orders and lines (interactive 471)
**Before this step:** No purchasing tables existed.
**What we did:** Defined `PurchaseOrder` and `PurchaseOrderLine`, reusing the existing Supplier, Product and custom user models.
**Code — `purchasing/models.py` (source excerpt):**
```python
class PurchaseOrder(models.Model):
    STATUS_CHOICES = [
        ('draft', 'Draft'),
        ('ordered', 'Ordered'),
        ('partially_received', 'Partially Received'),
        ('received', 'Received'),
        ('cancelled', 'Cancelled'),
    ]
    po_number = models.CharField(max_length=20, unique=True)
    supplier = models.ForeignKey('catalog.Supplier', on_delete=models.PROTECT)
    status = models.CharField(max_length=20, choices=STATUS_CHOICES, default='draft')
    expected_delivery_date = models.DateField(null=True, blank=True)
    created_by = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.PROTECT)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.po_number


class PurchaseOrderLine(models.Model):
    purchase_order = models.ForeignKey(PurchaseOrder, on_delete=models.PROTECT, related_name='lines')
    product = models.ForeignKey('catalog.Product', on_delete=models.PROTECT)
    quantity_ordered = models.PositiveIntegerField()
    unit_cost = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return self.product.name
```
**Walkthrough:** `po_number` uniquely identifies each order; `supplier` identifies who supplies it; `status` starts at `draft`; `created_by` records the employee. Each line links one product to its order with quantity and cost. `PROTECT` prevents deleting referenced records. These fields alone do not enforce a matching supplier, positive nonzero quantities, or valid status transitions; later service validation is necessary.
**How we checked it:** `purchasing/migrations/0001_initial.py` is present in the source commit.
**Next:** Register admin and test basic model behavior.

## Step 335 — Admin, model tests and migrations (interactive 472)
**Before this step:** Models existed but had no project-specific admin interface or tests.
**What we did:** Registered `PurchaseOrder` with inline `PurchaseOrderLine` editing; added eight model tests; generated and applied the initial migration.
**Code — `purchasing/admin.py`:**
```python
from django.contrib import admin
from .models import PurchaseOrder, PurchaseOrderLine


class PurchaseOrderLineInline(admin.TabularInline):
    model = PurchaseOrderLine
    extra = 1


@admin.register(PurchaseOrder)
class PurchaseOrderAdmin(admin.ModelAdmin):
    list_display = [
        'po_number', 'supplier', 'status',
        'expected_delivery_date', 'created_by', 'created_at'
    ]
    list_filter = ['status']
    search_fields = ['po_number', 'supplier__name']
    inlines = [PurchaseOrderLineInline]
```
**Code — `purchasing/tests.py` (representative test from source):**
```python
    def test_creating_order_does_not_change_stock(self):
        PurchaseOrderLine.objects.create(
            purchase_order=self.order,
            product=self.product,
            quantity_ordered=10,
            unit_cost=Decimal('40.00')
        )
        self.product.refresh_from_db()
        self.assertEqual(self.product.on_hand, 0)
```
**Walkthrough:** The inline lets an administrator enter order lines on the order page. Tests check order creation, draft status, string representations, line relationships, protected deletions, and that an order does not change stock. The `refresh_from_db()` call reads the latest saved stock before asserting it is unchanged.
**Code — terminal:**
```powershell
python manage.py makemigrations purchasing
python manage.py migrate
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test purchasing
```
**How we checked it:** User reported all checks passed; detailed terminal output was not supplied. The commit contains the migration and eight tests.
**Next:** Commit and push.

## Step 336 — Publish verified Chapter 1 (interactive 473)
**Before this step:** Purchasing changes were local.
**What we did:** Cleaned trailing whitespace, staged the Purchasing app, settings and migration, ran `git diff --cached --check`, committed and pushed.
**Code — terminal (key commands):**
```powershell
git diff --cached --check
git commit -m "Complete Module 5 Chapter 1 purchasing setup"
git push origin main
git status
```
**How we checked it:** GitHub independently confirms commit `45bd7a265a4b0eb8a3eac846582dede8d027b42f` on source `main`, including nine changed files. This verifies the remote commit, not the user's local working-tree status.
**Next:** Module 5 Chapter 2 — purchase order creation workflow, including supplier/product validation and controlled status changes.

## Chapter boundaries
- No goods receipt or incoming stock adjustment yet.
- No purchase-order form, protected user-facing view, or dedicated Purchasing URL yet.
- Django Admin is not a substitute for a role-restricted purchase workflow.
- SQLite does not provide the same row-level locking as databases supporting `SELECT FOR UPDATE`.
