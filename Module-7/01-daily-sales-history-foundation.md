# Module 7 — Chapter 1: Daily Sales History Foundation

## Chapter introduction

After the Core system passed its final checks, a more realistic dataset was seeded: 48 branded products and 3,291 sales, including 32 voided transactions. The new Stretch work begins by preparing *daily per-product sales history* from these existing transactions. This is preparation for AI forecasting, not forecasting or CSV importing yet.

**Source checkpoint:** [`21d51cf`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/21d51cf) on `main`. **Journal:** `study-2`.

## Before this chapter

The inventory application already stored Sale and SaleLine records with dates, quantities, product names and prices. But there was no reusable per-product time series for forecasting. Sales can be absent on some days; treating those missing days as absent rather than zero would distort later methods.

## Steps 383–391

### Step 383 — Create and register the intelligence Django app

**Before this step / purpose:** Ran `python manage.py startapp intelligence`, added `intelligence` in `INSTALLED_APPS`, then ran Django system check. The new app separates historical data and future AI work from inventory and sales business logic.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 384.

### Step 384 — Define the DailySales model

**Before this step / purpose:** Added a protected Product foreign key, indexed date, non-negative units_sold and decimal gross_revenue. A database uniqueness constraint enforces one row per product per day.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 385.

### Step 385 — Create and apply the migration

**Before this step / purpose:** Generated and applied `intelligence/migrations/0001_initial.py`. User reported `[X] 0001_initial`, no system-check issues and a passed stock-ledger check. The migration added a new empty summary table rather than altering Sales or Product.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 386.

### Step 386 — Calculate daily totals from completed sales

**Before this step / purpose:** Implemented `get_product_daily_sales`. It groups SaleLine records by local transaction date, sums quantity and gross line revenue and fills missing dates with zeros. Voided Sale records are excluded. User verified seven daily rows for Classmate Pulse Notebook, including zero-sale days.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 387.

### Step 387 — Store daily summaries without duplicate rows

**Before this step / purpose:** Implemented `save_product_daily_sales` using `update_or_create` in `transaction.atomic`. The user saved seven Classmate rows, verifying exactly seven persisted records.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 388.

### Step 388 — Test aggregation, void exclusion and resynchronization

**Before this step / purpose:** Added four TestCase methods for combining sales on the same date, filling zero-sale dates, excluding voided sales and updating existing summaries rather than inserting duplicates. Four tests passed in 5.144 seconds.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 389.

### Step 389 — Aggregate all products and dates

**Before this step / purpose:** Implemented `save_all_product_daily_sales`, which derives the start date from the oldest completed sale and end date from local today, then calls the per-product synchronizer. User confirmed 48 products, dates 2025-11-13 to 2026-11-13 inclusive and 17,568 summary rows.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 390.

### Step 390 — Verify full product coverage and synchronization

**Before this step / purpose:** Added fifth test for two products and three dates with repeat calls. Five tests passed in 1.751 seconds. Separately, user verified 48 products × 366 dates and exactly 17,568 DailySales records in their populated local database.

**How verified:** See the exact source, tests, and chapter-level verification below.

**Next:** Step 391.

### Step 391 — Run final checks, stage selected source and push

**Before this step / purpose:** User confirmed a successful source push `21d51cf` on `main`. Pre-push checks: five focused tests passed, no pending migration changes, 403 tests discovered, current ledger passed; the stage initially failed on two whitespace issues in the test file, which were cleaned before successful commit. **403 tests were discovered, not fully executed in this chapter**.

**How verified:** Source push and safeguards described above.

**Next:** Chapter 2 — CSV import and validation.


## Exact committed code and file paths

The following code is reproduced from the **actual `21d51cf` source files**, not reconstructed from memory.

**CLASS — `config/settings.py` (relevant installed-app section; other settings retained)**

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
    "intelligence",
]
```

**NEW, generated by Django — `intelligence/apps.py` (complete source)**

```python
from django.apps import AppConfig


class IntelligenceConfig(AppConfig):
    name = 'intelligence'
```

**CLASS — `intelligence/models.py` (complete source)**

```python
from django.db import models


class DailySales(models.Model):
    product = models.ForeignKey(
        'catalog.Product',
        on_delete=models.PROTECT,
        related_name='daily_sales'
    )

    date = models.DateField(db_index=True)

    units_sold = models.PositiveIntegerField(
        default=0
    )

    gross_revenue = models.DecimalField(
        max_digits=12,
        decimal_places=2,
        default=0
    )

    class Meta:
        constraints = [
            models.UniqueConstraint(
                fields=['product', 'date'],
                name='unique_daily_sales_product_date'
            )
        ]
        ordering = ['date', 'product_id']

    def __str__(self):
        return f'{self.product.name} - {self.date}'
```

**NEW — `intelligence/migrations/0001_initial.py` (complete generated migration)**

```python
# Generated by Django 6.0.9 on 2026-11-12 18:15

import django.db.models.deletion
from django.db import migrations, models


class Migration(migrations.Migration):

    initial = True

    dependencies = [
        ('catalog', '0002_product'),
    ]

    operations = [
        migrations.CreateModel(
            name='DailySales',
            fields=[
                ('id', models.BigAutoField(auto_created=True, primary_key=True, serialize=False, verbose_name='ID')),
                ('date', models.DateField(db_index=True)),
                ('units_sold', models.PositiveIntegerField(default=0)),
                ('gross_revenue', models.DecimalField(decimal_places=2, default=0, max_digits=12)),
                ('product', models.ForeignKey(on_delete=django.db.models.deletion.PROTECT, related_name='daily_sales', to='catalog.product')),
            ],
            options={
                'ordering': ['date', 'product_id'],
                'constraints': [models.UniqueConstraint(fields=('product', 'date'), name='unique_daily_sales_product_date')],
            },
        ),
    ]
```

**NEW — `intelligence/services.py` (complete source)**

```python
from datetime import date, timedelta
from decimal import Decimal

from django.db.models import Sum
from django.db.models.functions import TruncDate

from sales.models import SaleLine

from django.db import transaction

from catalog.models import Product
from .models import DailySales

from django.utils import timezone
from sales.models import Sale



def get_product_daily_sales(product_id, start_date, end_date):

    if (
        not isinstance(start_date, date)
        or not isinstance(end_date, date)
        or isinstance(start_date, __import__('datetime').datetime)
        or isinstance(end_date, __import__('datetime').datetime)
        or start_date > end_date
    ):
        raise ValueError('Invalid date range')

    sales = (
        SaleLine.objects.filter(
            product_id=product_id,
            sale__status='completed',
            sale__created_at__date__gte=start_date,
            sale__created_at__date__lte=end_date
        )
        .annotate(
            sale_day=TruncDate('sale__created_at')
        )
        .values('sale_day')
        .annotate(
            total_units=Sum('quantity'),
            total_revenue=Sum('line_total')
        )
    )

    sales_by_date = {
        item['sale_day']: item
        for item in sales
    }

    daily_data = []
    current_date = start_date

    while current_date <= end_date:
        record = sales_by_date.get(current_date)

        daily_data.append({
            'date': current_date,
            'units_sold': (
                record['total_units'] if record else 0
            ),
            'gross_revenue': (
                record['total_revenue']
                if record else Decimal('0.00')
            )
        })

        current_date += timedelta(days=1)

    return daily_data




def save_product_daily_sales(product_id, start_date, end_date):

    daily_data = get_product_daily_sales(
        product_id,
        start_date,
        end_date
    )

    product = Product.objects.get(pk=product_id)

    with transaction.atomic():

        for row in daily_data:
            DailySales.objects.update_or_create(
                product=product,
                date=row['date'],
                defaults={
                    'units_sold': row['units_sold'],
                    'gross_revenue': row['gross_revenue']
                }
            )

    return len(daily_data)




def save_all_product_daily_sales():

    first_sale = (
        Sale.objects.filter(
            status='completed'
        ).order_by('created_at').first()
    )

    if first_sale is None:
        raise ValueError('No completed sales available')

    start_date = timezone.localtime(
        first_sale.created_at
    ).date()

    end_date = timezone.localdate()

    product_count = 0
    total_records = 0

    products = Product.objects.all().order_by('pk')

    for product in products:
        saved = save_product_daily_sales(
            product.pk,
            start_date,
            end_date
        )

        product_count += 1
        total_records += saved

    return {
        'products': product_count,
        'start_date': start_date,
        'end_date': end_date,
        'records': total_records
    }
```

**NEW — `intelligence/test_daily_sales.py` (complete source)**

```python
from datetime import datetime, time, timedelta
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.utils import timezone

from catalog.models import Product, Supplier
from sales.models import Sale, SaleLine

from .models import DailySales
from .services import (
    get_product_daily_sales,
    save_product_daily_sales,
    save_all_product_daily_sales
)


class DailySalesTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='daily_manager',
            email='daily_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.supplier = Supplier.objects.create(
            name='Test Supplier'
        )

        self.product = Product.objects.create(
            sku='DAILY-001',
            name='Test Notebook',
            category='Stationery',
            supplier=self.supplier,
            unit_price=Decimal('50.00')
        )

        self.today = timezone.localdate()
        self.start_date = self.today - timedelta(days=2)

    def create_test_sale(
        self, number, day, quantity, status='completed'
    ):
        sale = Sale.objects.create(
            sale_number=f'DAILY-{number}',
            submission_key=f'daily-key-{number}',
            created_by=self.manager,
            status=status,
            subtotal=Decimal('50.00') * quantity,
            total=Decimal('50.00') * quantity
        )

        Sale.objects.filter(pk=sale.pk).update(
            created_at=timezone.make_aware(
                datetime.combine(day, time(12, 0))
            )
        )

        return SaleLine.objects.create(
            sale=sale,
            product=self.product,
            product_name=self.product.name,
            quantity=quantity,
            unit_price=Decimal('50.00'),
            line_total=Decimal('50.00') * quantity
        )

    def test_multiple_sales_on_same_day_are_combined(self):
        self.create_test_sale(1, self.start_date, 2)
        self.create_test_sale(2, self.start_date, 3)

        rows = get_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.start_date
        )

        self.assertEqual(len(rows), 1)
        self.assertEqual(rows[0]['units_sold'], 5)
        self.assertEqual(
            rows[0]['gross_revenue'],
            Decimal('250.00')
        )

    def test_days_without_sales_return_zero(self):
        self.create_test_sale(1, self.start_date, 2)

        rows = get_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.today
        )

        self.assertEqual(len(rows), 3)

        self.assertEqual(
            [row['units_sold'] for row in rows],
            [2, 0, 0]
        )

    def test_voided_sales_are_excluded(self):
        self.create_test_sale(
            1, self.start_date, 2
        )

        self.create_test_sale(
            2, self.start_date, 10,
            status='voided'
        )

        rows = get_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.start_date
        )

        self.assertEqual(rows[0]['units_sold'], 2)
        self.assertEqual(
            rows[0]['gross_revenue'],
            Decimal('100.00')
        )

    def test_saving_twice_does_not_duplicate_records(self):
        line = self.create_test_sale(
            1, self.start_date, 2
        )

        first_count = save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.today
        )

        second_count = save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.today
        )

        self.assertEqual(first_count, 3)
        self.assertEqual(second_count, 3)

        self.assertEqual(
            DailySales.objects.filter(
                product=self.product
            ).count(),
            3
        )

        line.quantity = 4
        line.line_total = Decimal('200.00')
        line.save(update_fields=[
            'quantity',
            'line_total'
        ])

        save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.today
        )

        record = DailySales.objects.get(
            product=self.product,
            date=self.start_date
        )

        self.assertEqual(record.units_sold, 4)
        self.assertEqual(
            record.gross_revenue,
            Decimal('200.00')
        )


    def test_all_products_sync_without_duplicates(self):
        second_product = Product.objects.create(
            sku='DAILY-002',
            name='Test Pen',
            category='Stationery',
            supplier=self.supplier,
            unit_price=Decimal('25.00')
        )

        self.create_test_sale(
            1, self.start_date, 2
        )

        first_result = save_all_product_daily_sales()

        self.assertEqual(first_result['products'], 2)
        self.assertEqual(first_result['start_date'], self.start_date)
        self.assertEqual(first_result['end_date'], self.today)
        self.assertEqual(first_result['records'], 6)

        self.assertEqual(DailySales.objects.count(), 6)

        second_result = save_all_product_daily_sales()

        self.assertEqual(second_result['records'], 6)
        self.assertEqual(DailySales.objects.count(), 6)

        second_product_units = list(
            DailySales.objects.filter(
                product=second_product
            ).order_by('date').values_list(
                'units_sold',
                flat=True
            )
        )

        self.assertEqual(
            second_product_units,
            [0, 0, 0]
        )
```

Other generated app files: `intelligence/__init__.py`, `intelligence/migrations/__init__.py`, `intelligence/admin.py`, `intelligence/tests.py`, and `intelligence/views.py` were staged for the committed Django app scaffold. They introduce no additional Chapter 1 domain logic.

## How the files work together

`config/settings.py` registers the new app, allowing Django to load its model and migrations. `DailySales` links each row to an existing `catalog.Product` through a protected foreign key and enforces one row per (`product`, `date`). The migration creates this table without changing historical sale rows.

`get_product_daily_sales` queries `sales.SaleLine`, excludes voided `Sale` records, groups by `TruncDate('sale__created_at')`, sums quantities and gross line revenue, then generates a complete continuous date range. For example, a day with no completed sale returns `units_sold=0` and `gross_revenue=0.00`.

`save_product_daily_sales` calls that read-only aggregation and persists each date via `update_or_create`. The `DailySales` unique constraint makes repeat synchronization safe from duplicate rows. `save_all_product_daily_sales` finds the earliest completed sale in the database and synchronizes **all** products to the local current date. Zero-sale dates are included. Note that future work should account for cache freshness after new transactions/voids; this chapter's summary table does not auto-update whenever a sale is recorded.

The tests isolate their own users, supplier, products, Sales and SaleLines in Django's test database. They directly check quantity and revenue aggregation, zeros, voids, and synchronization behavior; they never depend on the user's populated `db.sqlite3`.

## How we checked it

- Step 384: `python manage.py check` passed; `makemigrations intelligence --dry-run` previewed `Create model DailySales`.
- Step 385: `python manage.py makemigrations intelligence` generated the migration; `python manage.py migrate intelligence` applied it successfully; `showmigrations` showed `[X] 0001_initial`; stock ledger passed.
- Step 386: user printed seven daily Classmate Pulse Notebook rows, including zeros and correct ₹115-per-unit gross revenue.
- Step 387: user printed `Days saved: 7`, `Database records: 7`.
- Step 388: **4 tests passed in 5.144 seconds**.
- Step 389: user populated `DailySales` with **17,568 records** across **48 products** from **2025-11-13 through 2026-11-13** inclusive (366 days).
- Step 390: **5 tests passed in 1.751 seconds**. User independently confirmed **48 distinct products, 17,568 total rows, 366 dates per product** with the message `Historical coverage verified`.
- Step 391: `check_ledger` passed; total **403 tests discovered**, and the final source changes were pushed to `main` as `21d51cf`. Trailing whitespace and an extra EOF blank line were fixed before the commit. **No full 403-test run was reported**; follow testing cadence in later chapters.

**Database safety:** Chapter 1 added and populated `DailySales`; it did **not** delete the 3,291 real-demo (synthetic) sales, 48 products, stock movements, purchase orders or existing account records.

## Chapter summary / handoff

The DailySales foundation is operational and committed. The next small chapter should begin with CSV input format/validation, ensuring historical imports cannot silently corrupt stock, duplicate records or misrepresent completed sale transactions. The system should preserve `m6-complete`, keep `DailySales` meaning explicit, and continue one verified step at a time. Build the final UI after AI and data workflows are finished.

Previous: [Post-Core Step 382](../Post-Core/01-realistic-branded-dataset.md#step-382). Next: **Module 7 Chapter 2 (Step 392)**.

**Recall question:** Why must we insert a zero-sales day when building a time series instead of keeping only dates with transactions?
