# Module 6 — Chapter 9: Seed Data Verification and Full Regression Tests

## Before this chapter
Chapter 8 added `seed_demo_data`, with six safety checks but no complete simulated-year integration verification. The user then chose to discard old dummy inventory data while keeping all eight local user accounts. The local one-time reset succeeded; afterward the user ran the new seed command against the cleaned SQLite database, and both the automatic and an independent `check_ledger` check passed.

The local command output reported **1,085 sales across 365 days (12 November 2025 to 11 November 2026)**, three suppliers, 25 products, 32 purchase orders, 30 goods receipts and six high-volume days. This chapter verifies the generator end to end in Django's isolated test database and runs the full application suite.

## What we did
We added a dedicated seven-test integration module. Its `setUpTestData()` method calls the seed command once for the whole test class using two temporary test users, avoiding expensive regeneration before each test. The tests cover generated records, the entire year of sales dates, abnormal-volume days and day-of-week patterns, purchasing/receipts, balance continuity and transaction links, dashboard totals, and rejection of a repeat seed command. None of the tests replace or clear the user's populated working database.

## Step 380 — Verify the synthetic year and run all regression tests

### Before this step
The safety tests showed that the command refused inappropriate arguments and rolled back a simulated failure. They did not establish that the complete year produced the expected dates, report figures and stock balance history.

### What we did and why
We created an integration test against the real Django services, validated the simulation from opening stock to final purchase-order states, then explicitly counted discovered tests before running the full suite. Counting tests first also guards against the indentation/test-discovery issue encountered in Module 6 Chapter 6.

### Code

**NEW — `inventory/test_seed_integration.py`** (complete committed source, verified at source commit `42a5f74`):

```python
from datetime import timedelta
from io import StringIO

from django.contrib.auth import get_user_model
from django.core.management import call_command
from django.core.management.base import CommandError
from django.db.models import Count
from django.db.models.functions import TruncDate
from django.test import TestCase
from django.utils import timezone

from catalog.models import Product, Supplier
from core.dashboard import (
    get_daily_sales_last_seven_days,
    get_manager_summary,
    get_sales_staff_summary,
    get_top_selling_products
)
from inventory.models import StockMovement
from purchasing.models import (
    GoodsReceipt,
    GoodsReceiptLine,
    PurchaseOrder
)
from sales.models import Sale, SaleLine

from inventory.management.commands.seed_demo_data import (
    ANOMALY_DAY_NUMBERS
)


class SeedDemoDataIntegrationTests(TestCase):

    @classmethod
    def setUpTestData(cls):
        User = get_user_model()

        cls.manager = User.objects.create_user(
            username='integration_manager',
            email='integration_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        cls.sales_user = User.objects.create_user(
            username='integration_sales',
            email='integration_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        cls.today = timezone.localdate()
        cls.first_day = cls.today - timedelta(days=365)
        cls.last_day = cls.today - timedelta(days=1)

        cls.seed_output = StringIO()

        call_command(
            'seed_demo_data',
            manager=cls.manager.username,
            stdout=cls.seed_output
        )

    def test_products_suppliers_and_users(self):
        User = get_user_model()

        self.assertEqual(User.objects.count(), 2)
        self.assertEqual(Supplier.objects.count(), 3)
        self.assertEqual(Product.objects.count(), 25)

        self.assertTrue(
            self.manager.check_password('TestPassword123!')
        )

        self.assertTrue(
            self.sales_user.check_password('TestPassword123!')
        )

        categories = set(
            Product.objects.values_list(
                'category',
                flat=True
            )
        )

        self.assertEqual(categories, {
            'Stationery',
            'Office Supplies',
            'Electronics',
            'Cleaning',
            'Pantry'
        })

        self.assertEqual(
            Product.objects.filter(
                sku__startswith='DEMO-'
            ).count(),
            25
        )

        self.assertEqual(
            StockMovement.objects.filter(
                movement_type='opening'
            ).count(),
            25
        )

    def test_sales_cover_all_365_days(self):
        self.assertEqual(Sale.objects.count(), 1085)

        self.assertEqual(
            Sale.objects.filter(
                status='completed'
            ).count(),
            1085
        )

        days = list(
            Sale.objects.annotate(
                sale_day=TruncDate('created_at')
            ).values_list(
                'sale_day',
                flat=True
            ).distinct().order_by('sale_day')
        )

        expected_days = [
            self.first_day + timedelta(days=number)
            for number in range(365)
        ]

        self.assertEqual(days, expected_days)

        self.assertGreater(
            SaleLine.objects.count(),
            Sale.objects.count()
        )

        self.assertFalse(
            Sale.objects.filter(
                created_at__date__gte=self.today
            ).exists()
        )

    def test_six_anomaly_days_and_weekly_patterns(self):
        daily_counts = {
            item['sale_day']: item['count']
            for item in Sale.objects.annotate(
                sale_day=TruncDate('created_at')
            ).values(
                'sale_day'
            ).annotate(
                count=Count('id')
            )
        }

        expected_anomalies = {
            self.first_day + timedelta(days=number)
            for number in ANOMALY_DAY_NUMBERS
        }

        actual_anomalies = {
            day
            for day, count in daily_counts.items()
            if count >= 12
        }

        self.assertEqual(
            actual_anomalies,
            expected_anomalies
        )

        for day, count in daily_counts.items():
            if day in expected_anomalies:
                self.assertIn(count, [12, 13, 14])

            elif day.weekday() >= 5:
                self.assertIn(count, [3, 4])

            else:
                self.assertIn(count, [2, 3])

    def test_purchase_orders_and_goods_receipts(self):
        self.assertEqual(
            PurchaseOrder.objects.count(),
            32
        )

        self.assertEqual(
            GoodsReceipt.objects.count(),
            30
        )

        self.assertEqual(
            PurchaseOrder.objects.filter(
                status='received'
            ).count(),
            29
        )

        self.assertEqual(
            PurchaseOrder.objects.filter(
                status='ordered'
            ).count(),
            2
        )

        self.assertEqual(
            PurchaseOrder.objects.filter(
                status='partially_received'
            ).count(),
            1
        )

        orders = PurchaseOrder.objects.prefetch_related(
            'lines'
        )

        for order in orders:
            for line in order.lines.all():
                self.assertLessEqual(
                    line.quantity_received,
                    line.quantity_ordered
                )

                if order.status == 'received':
                    self.assertEqual(
                        line.quantity_received,
                        line.quantity_ordered
                    )

                elif order.status == 'ordered':
                    self.assertEqual(
                        line.quantity_received,
                        0
                    )

        open_orders = PurchaseOrder.objects.filter(
            status__in=['ordered', 'partially_received']
        )

        expected_dates = {
            self.last_day - timedelta(days=3),
            self.last_day - timedelta(days=2),
            self.last_day + timedelta(days=7)
        }

        actual_dates = set(
            open_orders.values_list(
                'expected_delivery_date',
                flat=True
            )
        )

        self.assertEqual(
            actual_dates,
            expected_dates
        )

    def test_stock_ledger_and_transaction_links(self):
        output = StringIO()

        call_command(
            'check_ledger',
            stdout=output
        )

        self.assertIn(
            'Stock ledger check passed',
            output.getvalue()
        )

        self.assertEqual(
            StockMovement.objects.filter(
                movement_type='sale'
            ).count(),
            SaleLine.objects.count()
        )

        self.assertEqual(
            StockMovement.objects.filter(
                movement_type='receipt'
            ).count(),
            GoodsReceiptLine.objects.filter(
                quantity_received__gt=0
            ).count()
        )

        for product in Product.objects.all():
            movements = StockMovement.objects.filter(
                product=product
            ).order_by(
                'created_at',
                'pk'
            )

            previous_balance = 0

            for movement in movements:
                self.assertEqual(
                    movement.balance_before,
                    previous_balance
                )

                self.assertEqual(
                    movement.balance_after,
                    movement.balance_before
                    + movement.quantity_delta
                )

                previous_balance = movement.balance_after

            self.assertEqual(
                product.on_hand,
                previous_balance
            )

            self.assertGreaterEqual(
                product.on_hand,
                0
            )

    def test_dashboards_use_generated_history(self):
        manager_summary = get_manager_summary()

        self.assertEqual(
            manager_summary['sales_today_count'],
            0
        )

        self.assertGreater(
            manager_summary['sales_week_count'],
            0
        )

        daily_sales = get_daily_sales_last_seven_days()

        self.assertEqual(len(daily_sales), 7)

        self.assertEqual(
            daily_sales[-1]['date'],
            self.today
        )

        self.assertEqual(
            daily_sales[-1]['sales_count'],
            0
        )

        self.assertEqual(
            sum(
                item['sales_count']
                for item in daily_sales
            ),
            manager_summary['sales_week_count']
        )

        self.assertGreater(
            len(get_top_selling_products()),
            0
        )

        staff_summary = get_sales_staff_summary(
            self.sales_user
        )

        self.assertGreater(
            staff_summary['week_count'],
            0
        )

        self.assertEqual(
            staff_summary['week_count'],
            manager_summary['sales_week_count']
        )

    def test_second_seed_run_is_rejected(self):
        sales_before = Sale.objects.count()
        products_before = Product.objects.count()
        orders_before = PurchaseOrder.objects.count()

        with self.assertRaisesMessage(
            CommandError,
            'Sales already exist'
        ):
            call_command(
                'seed_demo_data',
                manager=self.manager.username,
                stdout=StringIO()
            )

        self.assertEqual(
            Sale.objects.count(),
            sales_before
        )

        self.assertEqual(
            Product.objects.count(),
            products_before
        )

        self.assertEqual(
            PurchaseOrder.objects.count(),
            orders_before
        )
```

### Code walkthrough
1. **One shared seed operation.** `setUpTestData` creates one Manager and one Sales Staff test account and invokes `call_command('seed_demo_data', manager=...)`. Django creates an isolated test database and preserves the generated fixture for the class. This does **not** call the seed command against local `db.sqlite3`.
2. **Product and account verification.** The first test confirms the two test users retain usable passwords, and that there are exactly three suppliers, 25 demo products, five category labels and 25 opening stock movements.
3. **Continuous sales history.** The second test verifies 1,085 completed sales on every day of the 365-day window, with no sale accidentally timestamped today. This is essential for date-filtered dashboard totals.
4. **Planned anomalies.** The third test compares the six days defined in `ANOMALY_DAY_NUMBERS` to the six days with unusually high sale counts. Other days must match the command's normal weekday/weekend count bands.
5. **Purchasing integrity.** The fourth test checks the exact 32 purchase orders and 30 goods receipts, including 29 received POs, two ordered POs and one partially received PO. It checks received quantities never exceed quantities ordered and validates the three open orders' expected dates.
6. **Stock history consistency.** The fifth test runs `check_ledger`, compares sale-line and receipt-line counts with movement counts, then checks each product's movement-by-movement before/after balances against its final `on_hand` quantity.
7. **Dashboard compatibility.** The sixth test checks Manager and Sales metrics and the seven-day daily sales series use the synthetic sales as expected. Because the simulation stops yesterday, sales today is zero.
8. **Repeat-run protection.** The seventh test confirms a second invocation is rejected without changing the existing synthetic sale, product or order counts.

### How we checked it
The user's completed PowerShell output established:

- `python manage.py test inventory.test_seed_integration --noinput`: **7 tests passed** in **8.661 seconds**.
- Django test discovery: **374 tests found**, matching the expected total and confirming no tests silently disappeared.
- `python manage.py test --noinput`: **all 374 tests passed** in **827.910 seconds**, zero Django system-check issues.
- `python manage.py check_ledger`: **Stock ledger check passed** on the user's already-populated SQLite database.
- `python manage.py check`: **zero issues**.
- `python manage.py makemigrations --check --dry-run`: **No changes detected**.
- Stage/commit/push completed with only the new integration test file. Verified source `main` commit: **`42a5f74`**.

**Important distinction:** Both the integration tests and the full suite run in temporary Django test databases. The user's populated SQLite dataset was independently checked through `check_ledger`, but the full test suite did not rewrite it.

### Where to go next
Previous: [Step 379](08-synthetic-historical-data-seed.md#step-379). Next: **Module 6 Chapter 10** — final URL authorization review, module closure checks, full regression suite and `m6-complete` tag. Preserve the user's existing eight accounts and seeded business data.

## Chapter summary
The 365-day demo data generator has now passed seven integration tests, all 374 application tests passed, and the user's populated SQLite stock ledger passed independent verification. This is the Chapter 9 full regression milestone; no business-data reset or second seed run is required.

**Recall question:** Why do these tests generate fresh demo data inside `setUpTestData` rather than testing the user's local `db.sqlite3`?

**Source:** [`42a5f74` — Verify historical seed data](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/42a5f74).
