# Module 4 — Chapter 1: Sales Setup

**Journal Steps 295–306 · Interactive Steps 428–444**  
**Verified source:** [initial commit `18022db`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/18022db) and [URL correction `7af5b5e`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/7af5b5e) on `main`. User terminal: **114 tests passed**, no pending migrations, clean status output.  
**Scope:** App registration, models, migration, admin, schema tests and URL skeleton only. **No sales recording, stock deduction, discounts calculation, voiding or sales dashboard is implemented in this chapter.**

## Before this chapter

Inventory already supported products, suppliers and stock movements. A sale will eventually reduce stock and record what the customer bought. Before implementing that transaction logic, we need a dedicated Django app and a stable database structure. This chapter also documents an early migration mistake and its repair so the final schema is clear.

<a id="step-295"></a>
## Step 295 — Create the Sales app (interactive Step 428)

### Before this step
Before this step, the project had inventory and accounts but no separate place for sales records.

### What we did and why
We ran `python manage.py startapp sales`. Django generated a dedicated app so sales models, views, tests and admin registration could grow without being mixed into inventory.

**Filename: `sales/apps.py`** — verified source excerpt:
```python
from django.apps import AppConfig


class SalesConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'sales'
```

### Code walkthrough
`SalesConfig` identifies the new Django app. The generated `__init__.py` makes the folder a Python package; `views.py` and `tests.py` are starting files, not working sales pages.

### How we checked it
The `sales/` app files are present in source commit `18022db`.

### Next
Register the app in settings.

<a id="step-296"></a>
## Step 296 — Register Sales in INSTALLED_APPS (interactive Step 429)

### Before this step
Creating a folder alone does not make Django discover its models and migrations.

### What we did and why
We added `sales` to the existing application list.

**Filename: `config/settings.py`** — verified source excerpt:
```python
    "catalog",
    "inventory",
    "sales",
]
```

### Code walkthrough
`INSTALLED_APPS` controls app discovery. The trailing comma is normal Python list syntax. `sales` must match the app's Python package name.

### How we checked it
The pushed settings file contains the line, and the final `manage.py check` passed.

### Next
Prepare the app's URLs.

<a id="step-297"></a>
## Step 297 — Prepare and connect Sales URL configuration (interactive Steps 430–431, 444)

### Before this step
The app needed its own place for URL routes and a connection from the project URL dispatcher.

### What we did and why
We created the empty Sales URL configuration and included it in the project routes. The first source commit accidentally omitted these changes; interactive Step 444 fixed and pushed them.

**Filename: `sales/urls.py`** — verified source excerpt:
```python
from django.urls import path
from . import views

urlpatterns = [
]
```

### Code walkthrough
`path` will define future routes. `views` is imported but has no Sales handlers yet. An empty list is valid: it does not create any Sales pages.

### How we checked it
Both `sales/urls.py` and `config/urls.py` are present at commit `7af5b5e`; the full test suite passed afterward.

### Next
Define database records.

<a id="step-298"></a>
## Step 298 — Draft the first Sale and SaleItem models (interactive Steps 432–433)

### Before this step
Before a sales workflow can save purchases, Django needs database models.

### What we did and why
We first drafted a small Sale model and a SaleItem model. Those were temporary learning versions, not the final schema. The approved design requires `SaleLine` and `created_by`, not `SaleItem` and `sold_by`.

**Filename: `sales/models.py`** — verified source excerpt:
```python
class Sale(models.Model):
    STATUS_CHOICES = [
        ('completed', 'Completed'),
        ('voided', 'Voided'),
    ]
```

### Code walkthrough
This is the final source's `Sale` class beginning, shown instead of reconstructing uncommitted draft code. The choices restrict the intended transaction states to completed and voided.

### How we checked it
The final model exists at `18022db`; the earlier drafts were superseded before publishing.

### Next
Correct the complete model schema.

<a id="step-299"></a>
## Step 299 — Replace draft models with the approved schema (interactive Steps 438–439)

### Before this step
The initial draft lacked discounts, transaction identifiers, void metadata, snapshots and the correct field names.

### What we did and why
We replaced the draft with final `Sale` and `SaleLine` definitions and corrected the admin imports. `Sale` is one transaction; `SaleLine` is one product row in that transaction.

**Filename: `sales/models.py`** — verified source excerpt:
```python
    sale_number = models.CharField(max_length=20, unique=True)
    submission_key = models.CharField(max_length=36, unique=True)
    created_by = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.PROTECT,
        related_name='created_sales'
    )
```

### Code walkthrough
`sale_number` is the business identifier; `submission_key` is reserved for duplicate-submission protection in a later service. `created_by` links the sale to a user and `PROTECT` preserves its history. The model itself does not yet implement idempotency.

### How we checked it
The final schema and generated migration agree in commit `18022db`.

### Next
Review money and void fields.

<a id="step-300"></a>
## Step 300 — Understand discounts, totals and void metadata (interactive Steps 438–439)

### Before this step
A sale needs to preserve the amount charged and record any later cancellation without deleting history.

### What we did and why
We added discount choices and values, subtotal/total, status, voided-by, voided-at and void reason.

**Filename: `sales/models.py`** — verified source excerpt:
```python
    discount_type = models.CharField(
        max_length=10,
        choices=DISCOUNT_CHOICES,
        default='none'
    )
    discount_value = models.DecimalField(
        max_digits=10, decimal_places=2, default=0
    )
    subtotal = models.DecimalField(
        max_digits=12, decimal_places=2, default=0
    )
    total = models.DecimalField(
        max_digits=12, decimal_places=2, default=0
    )
```

### Code walkthrough
`DecimalField` stores money at two decimal places. The choices allow none, fixed and percent. These are storage fields only: discount arithmetic and permission checks still need service and view code.

### How we checked it
The fields are present in the committed model and migration.

### Next
Understand sale lines and price snapshots.

<a id="step-301"></a>
## Step 301 — Define SaleLine and snapshots (interactive Steps 438–439)

### Before this step
A transaction can contain several products. A product's name or price may later change, but an old receipt must keep its original values.

### What we did and why
We defined `SaleLine` with product link, product-name snapshot, quantity, unit-price snapshot and calculated line-total storage.

**Filename: `sales/models.py`** — verified source excerpt:
```python
class SaleLine(models.Model):
    sale = models.ForeignKey(
        Sale,
        on_delete=models.PROTECT,
        related_name='lines'
    )
    product = models.ForeignKey(
        'catalog.Product',
        on_delete=models.PROTECT
    )
    product_name = models.CharField(max_length=200)
    quantity = models.PositiveIntegerField()
```

### Code walkthrough
`related_name='lines'` lets later code access a sale's rows with `sale.lines`. The product FK points to the live catalog record, while `product_name` stores the historical text. A later service must fill snapshots correctly.

### How we checked it
`SaleLine` and its `PROTECT` relationships appear in the committed source.

### Next
Generate database migration.

<a id="step-302"></a>
## Step 302 — Repair and apply the initial Sales migration (interactive Steps 434–435, 438–440)

### Before this step
The first migration was applied against the temporary model. Adding the required `created_by` field afterward caused Django to ask for a default for existing rows.

### What we did and why
We stopped that migration attempt. During this early setup, the user removed only the Sales tables, cleared Sales migration history, deleted the outdated Sales migration file and regenerated a clean initial migration from the corrected models.

**Filename: `sales/migrations/0001_initial.py`** — verified source excerpt:
```python
    operations = [
        migrations.CreateModel(
            name='Sale',
```

### Code walkthrough
`makemigrations` creates migration instructions, and `migrate` applies them. This reset was an exceptional local-development repair, not a normal deployment procedure. Never drop Sales tables once they hold real transactions.

### How we checked it
The final migration contains `CreateModel` for `Sale` and `SaleLine`; the user confirmed migration and checks succeeded.

### Next
Register models in Django Admin.

<a id="step-303"></a>
## Step 303 — Register both Sales models in Django Admin (interactive Steps 436–437, 438–439)

### Before this step
Developers need a simple way to inspect records during setup; no custom Sales pages exist yet.

### What we did and why
We registered both models in `sales/admin.py`. The original registration of `SaleItem` was corrected to `SaleLine`.

**Filename: `sales/admin.py`** — verified source excerpt:
```python
from django.contrib import admin
from .models import Sale, SaleLine

admin.site.register(Sale)
admin.site.register(SaleLine)
```

### Code walkthrough
`admin.site.register` exposes a model through Django's built-in administration. It does not grant sales staff access or implement a custom sales workflow.

### How we checked it
The final file contains exactly these imports and registrations.

### Next
Add automated structure checks.

<a id="step-304"></a>
## Step 304 — Test Sales model fields and protected relationships (interactive Steps 441–442)

### Before this step
A successful migration does not prove that all required fields were included or that deletion behavior is safe.

### What we did and why
We added three tests checking the Sale field names, SaleLine field names and `PROTECT` on its sale/product relationships.

**Filename: `sales/tests.py`** — verified source excerpt:
```python
    def test_sale_line_relationships_are_protected(self):
        for field_name in ['sale', 'product']:
            field = SaleLine._meta.get_field(field_name)
            self.assertEqual(field.remote_field.on_delete, models.PROTECT)
```

### Code walkthrough
`_meta.get_field()` inspects the model definition. The assertion ensures the two references cannot be changed to cascading deletes unnoticed. These tests inspect schema only; they do not test checkout, overselling, rollback or voiding.

### How we checked it
The user reported 114 tests passed, including three Sales model tests.

### Next
Publish source code.

<a id="step-305"></a>
## Step 305 — Test and push Sales Setup (interactive Step 443)

### Before this step
The Sales app, migration and structure tests existed locally but needed a reproducible checkpoint on GitHub.

### What we did and why
We ran Django checks, migration dry-run, full tests, staged Sales and settings, committed and pushed.

**Filename: `terminal (PowerShell)`** — terminal commands:
```powershell
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
git add config/settings.py config/urls.py sales/
git commit -m "Complete Module 4 Chapter 1 sales setup"
git push origin main
```

### Code walkthrough
The check catches configuration problems; the migration dry-run catches unrecorded model changes; the test suite checks regressions. Git stages, saves and publishes the changes. The URL files were missing from this first commit, which we corrected next.

### How we checked it
The user pasted 114 tests passing and commit `18022db` pushed. The commit included nine files and omitted the Sales URL connection.

### Next
Complete the missing URL integration.

<a id="step-306"></a>
## Step 306 — Correct omitted URLs and close the source checkpoint (interactive Step 444)

### Before this step
The earlier terminal staging command did not include `config/urls.py` or the new `sales/urls.py`, so the app was installed but not yet connected to the project's URL configuration.

### What we did and why
We added the Sales include to the existing project URLs, created the Sales URL file, reran checks and the full test suite, and pushed a follow-up commit.

**Filename: `config/urls.py`** — verified source excerpt:
```python
    path('', include('inventory.urls')),
    path('', include('sales.urls')),
]
```

### Code walkthrough
`include('sales.urls')` delegates matching to the Sales app. Because its own patterns are empty, it introduces no new accessible Sales page yet. This is correct for the setup chapter.

### How we checked it
The user pasted 114 tests passing and commit `7af5b5e` pushed to `main`. GitHub source confirms both URL files.

### Next
Next chapter: implement Sales transaction services, forms and views; do not claim these are already working.

