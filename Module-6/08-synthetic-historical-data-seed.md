# Module 6 — Chapter 8: Synthetic Historical Data Seed Command

## Before this chapter

The three role dashboards are complete, but the existing local SQLite data contained only a few throwaway manual-test entries. The user elected to clear all business data while **preserving eight user accounts**. Their local reset script confirmed it deleted products, supplier records, sales, stock movements, purchase orders, receipts and line items; it made a SQLite backup before deleting, and all users, roles and passwords stayed intact.

Chapter 8 provides a reproducible way to populate this empty local database with realistic demonstration records *without making artificial Django ORM records that bypass the existing business rules*.

## What we did

Added a Django management command, `seed_demo_data`, for a year of dated sales and inventory activity, plus a test file for its refusal/rollback behavior. The command creates **three suppliers, 25 products in five categories**, opening stock, purchasing/receipts and **365 historical days ending yesterday**. Weekends use a different daily sales rate and six specified days have amplified sales. It uses a fixed random generator seed `2026`. UUID-generated sale/receipt numbers intentionally remain non-deterministic.

A command-line `--manager` argument identifies an **existing active Manager**; no new user accounts are created. If any `Sale` exists or if demo SKUs/order numbers already exist, execution stops. Work is wrapped in an atomic transaction; `check_ledger` must succeed for the transaction to commit.

## Step 379 — Write the seed command and test its safety

### Before this step

Dashboard views depend on historical `created_at` data. The user's previous manual entries were not helpful for viewing sales trends. We needed synthetic history while preserving the normal stock ledger and supplier/PO relationships.

### Code

**NEW — `inventory/management/commands/seed_demo_data.py`** (complete committed source):

```python
from collections import defaultdict
from datetime import datetime, time, timedelta
from decimal import Decimal
import random

from django.contrib.auth import get_user_model
from django.core.management import call_command
from django.core.management.base import BaseCommand, CommandError
from django.db import transaction
from django.utils import timezone

from catalog.models import Product, Supplier
from inventory.models import StockMovement
from inventory.services import change_stock
from purchasing.models import GoodsReceipt, PurchaseOrder
from purchasing.services import (
    change_purchase_order_status,
    create_purchase_order,
    receive_goods
)
from sales.models import Sale
from sales.services import record_sale


PRODUCT_DATA = [
    ('Stationery', 'A5 Notebook', '55.00'),
    ('Stationery', 'Blue Ballpoint Pen', '12.00'),
    ('Stationery', 'Black Gel Pen', '25.00'),
    ('Stationery', 'HB Pencil', '8.00'),
    ('Stationery', 'White Eraser', '10.00'),
    ('Office Supplies', 'A4 Printer Paper', '320.00'),
    ('Office Supplies', 'Desktop Stapler', '110.00'),
    ('Office Supplies', 'Staple Pins Box', '30.00'),
    ('Office Supplies', 'File Folder', '35.00'),
    ('Office Supplies', 'Sticky Notes', '65.00'),
    ('Electronics', 'USB C Cable', '180.00'),
    ('Electronics', 'Wireless Mouse', '650.00'),
    ('Electronics', 'USB Keyboard', '480.00'),
    ('Electronics', '32GB USB Drive', '520.00'),
    ('Electronics', 'Laptop Stand', '900.00'),
    ('Cleaning', 'Floor Cleaner', '140.00'),
    ('Cleaning', 'Liquid Hand Wash', '95.00'),
    ('Cleaning', 'Tissue Box', '75.00'),
    ('Cleaning', 'Microfiber Cloth', '85.00'),
    ('Cleaning', 'Trash Bags Pack', '130.00'),
    ('Pantry', 'Tea Bags Box', '160.00'),
    ('Pantry', 'Instant Coffee Jar', '290.00'),
    ('Pantry', 'Sugar Pack', '58.00'),
    ('Pantry', 'Paper Cups Pack', '100.00'),
    ('Pantry', 'Biscuit Pack', '45.00')
]

ANOMALY_DAY_NUMBERS = (34, 96, 158, 220, 282, 344)


class Command(BaseCommand):
    help = 'Create 365 days of reproducible local demo inventory and sales history'

    def add_arguments(self, parser):
        parser.add_argument(
            '--manager',
            help='Username of an existing active Inventory Manager'
        )

    def timestamp(self, day, hour, minute=0):
        return timezone.make_aware(
            datetime.combine(day, time(hour, minute))
        )

    def handle(self, *args, **options):
        username = (options.get('manager') or '').strip()

        if not username:
            raise CommandError('Provide --manager with an existing username.')

        User = get_user_model()
        manager = User.objects.filter(
            username=username,
            role='manager',
            is_active=True
        ).first()

        if manager is None:
            raise CommandError('An active Inventory Manager is required.')

        if Sale.objects.exists():
            raise CommandError(
                'Sales already exist. Use a fresh demo database; '
                'existing sales will not be altered.'
            )

        if Product.objects.filter(sku__startswith='DEMO-').exists():
            raise CommandError('Demo product SKUs already exist.')

        if PurchaseOrder.objects.filter(
            po_number__startswith='DEMO-PO-'
        ).exists():
            raise CommandError('Demo purchase order numbers already exist.')

        first_day = timezone.localdate() - timedelta(days=365)
        last_day = first_day + timedelta(days=364)
        rng = random.Random(2026)

        try:
            with transaction.atomic():
                suppliers = self.create_suppliers()
                products = self.create_products(
                    suppliers, manager, first_day, rng
                )

                sales_people = list(
                    User.objects.filter(
                        role='sales', is_active=True
                    ).order_by('pk')
                )

                if not sales_people:
                    sales_people = [manager]

                po_number = 0
                sale_count = 0
                receipt_count = 0
                anomaly_days = []

                for day_number in range(365):
                    day = first_day + timedelta(days=day_number)
                    is_anomaly = day_number in ANOMALY_DAY_NUMBERS

                    if is_anomaly:
                        anomaly_days.append(day)
                        sale_total = 12 + rng.randint(0, 2)
                    elif day.weekday() >= 5:
                        sale_total = 3 + rng.randint(0, 1)
                    else:
                        sale_total = 2 + rng.randint(0, 1)

                    planned_sales = []
                    required = defaultdict(int)

                    for sale_index in range(sale_total):
                        selection = rng.sample(
                            products, rng.randint(1, 3)
                        )
                        items = []

                        for product in selection:
                            quantity = rng.randint(1, 3)

                            if is_anomaly:
                                quantity *= 3

                            items.append({
                                'product_id': product.pk,
                                'quantity': quantity
                            })

                            required[product.pk] += quantity

                        planned_sales.append(items)

                    stock = dict(
                        Product.objects.filter(
                            pk__in=[p.pk for p in products]
                        ).values_list('pk', 'on_hand')
                    )

                    orders_by_supplier = defaultdict(list)

                    for product in products:
                        available = stock[product.pk]
                        needed = required[product.pk]

                        regular_restock = (
                            day_number % 7 == 0 and available < 120
                        )

                        shortage_risk = available < needed + 10

                        if regular_restock or shortage_risk:
                            target = max(220, needed + 60)
                            quantity = target - available

                            if quantity > 0:
                                orders_by_supplier[product.supplier_id].append({
                                    'product_id': product.pk,
                                    'quantity': quantity,
                                    'unit_cost': str(
                                        (
                                            product.unit_price
                                            * Decimal('0.75')
                                        ).quantize(Decimal('0.01'))
                                    )
                                })

                    for supplier_id, order_items in orders_by_supplier.items():
                        po_number += 1

                        order = create_purchase_order(
                            po_number=f'DEMO-PO-{po_number:05d}',
                            supplier_id=supplier_id,
                            items=order_items,
                            user=manager
                        )

                        change_purchase_order_status(
                            order.pk, 'ordered', manager
                        )

                        PurchaseOrder.objects.filter(pk=order.pk).update(
                            created_at=self.timestamp(
                                day - timedelta(days=3), 9
                            ),
                            expected_delivery_date=day
                        )

                        receipt_items = [
                            {
                                'product_id': item['product_id'],
                                'quantity_received': item['quantity'],
                                'quantity_damaged': 0
                            }
                            for item in order_items
                        ]

                        receipt = receive_goods(
                            order_id=order.pk,
                            items=receipt_items,
                            submission_key=f'demo-receipt-{po_number:05d}',
                            user=manager
                        )

                        received_time = self.timestamp(day, 9)

                        GoodsReceipt.objects.filter(pk=receipt.pk).update(
                            received_at=received_time
                        )

                        StockMovement.objects.filter(
                            source=receipt.receipt_number,
                            movement_type='receipt'
                        ).update(created_at=received_time)

                        receipt_count += 1

                    for sale_index, items in enumerate(planned_sales):
                        seller = sales_people[
                            (day_number + sale_index) % len(sales_people)
                        ]

                        has_discount = (
                            (day_number + sale_index) % 17 == 0
                        )

                        sale = record_sale(
                            items=items,
                            user=seller,
                            submission_key=(
                                f'demo-{day_number:03d}-{sale_index:02d}'
                            ),
                            discount_type=(
                                'percent' if has_discount else 'none'
                            ),
                            discount_value=(5 if has_discount else 0)
                        )

                        sold_at = self.timestamp(
                            day, 11, 0
                        ) + timedelta(minutes=sale_index * 20)

                        Sale.objects.filter(pk=sale.pk).update(
                            created_at=sold_at
                        )

                        StockMovement.objects.filter(
                            source=sale.sale_number,
                            movement_type='sale'
                        ).update(created_at=sold_at)

                        sale_count += 1

                po_number, extra_receipts = self.create_open_orders(
                    suppliers,
                    products,
                    manager,
                    last_day,
                    po_number
                )

                receipt_count += extra_receipts

                # A failing check aborts the outer transaction.
                call_command('check_ledger', stdout=self.stdout)

                self.stdout.write(self.style.SUCCESS(
                    f'Seeded {sale_count} sales over 365 days '
                    f'({first_day} to {last_day}).'
                ))

                self.stdout.write(
                    f'Created 3 suppliers, {len(products)} products, '
                    f'{po_number} purchase orders and '
                    f'{receipt_count} goods receipts.'
                )

                self.stdout.write(
                    'Six high-sales days: ' +
                    ', '.join(str(day) for day in anomaly_days)
                )

        except ValueError as error:
            raise CommandError(str(error)) from error

    def create_suppliers(self):
        names = [
            'Demo Office Supply Co',
            'Demo Technology Warehouse',
            'Demo Daily Essentials'
        ]

        return [
            Supplier.objects.create(name=name)
            for name in names
        ]

    def create_products(self, suppliers, manager, first_day, rng):
        products = []

        for number, (category, name, price) in enumerate(
            PRODUCT_DATA, 1
        ):
            if category in ['Stationery', 'Office Supplies']:
                supplier = suppliers[0]
            elif category == 'Electronics':
                supplier = suppliers[1]
            else:
                supplier = suppliers[2]

            product = Product.objects.create(
                sku=f'DEMO-{number:03d}',
                name=name,
                category=category,
                supplier=supplier,
                unit_price=Decimal(price),
                safety_stock=12 + number % 9,
                lead_time_days=2 + number % 6
            )

            initial_quantity = 80 + rng.randint(0, 30)

            movement = change_stock(
                product_id=product.pk,
                delta=initial_quantity,
                movement_type='opening',
                reason='Demo opening inventory',
                user=manager,
                source=f'demo-opening-{number:03d}'
            )

            StockMovement.objects.filter(pk=movement.pk).update(
                created_at=self.timestamp(first_day, 8)
            )

            products.append(product)

        return products

    def create_open_orders(
        self, suppliers, products, manager, last_day, po_number
    ):
        receipt_count = 0

        examples = [
            (
                suppliers[0],
                'ordered',
                last_day - timedelta(days=3)
            ),
            (
                suppliers[1],
                'ordered',
                last_day + timedelta(days=7)
            ),
            (
                suppliers[2],
                'partially_received',
                last_day - timedelta(days=2)
            )
        ]

        for supplier, status, expected in examples:
            product = next(
                p for p in products
                if p.supplier_id == supplier.pk
            )

            po_number += 1

            order = create_purchase_order(
                po_number=f'DEMO-PO-{po_number:05d}',
                supplier_id=supplier.pk,
                items=[{
                    'product_id': product.pk,
                    'quantity': 120,
                    'unit_cost': str(
                        (
                            product.unit_price
                            * Decimal('0.75')
                        ).quantize(Decimal('0.01'))
                    )
                }],
                user=manager
            )

            change_purchase_order_status(
                order.pk, 'ordered', manager
            )

            PurchaseOrder.objects.filter(pk=order.pk).update(
                created_at=self.timestamp(
                    last_day - timedelta(days=14), 9
                ),
                expected_delivery_date=expected
            )

            if status == 'partially_received':
                receipt = receive_goods(
                    order_id=order.pk,
                    items=[{
                        'product_id': product.pk,
                        'quantity_received': 20,
                        'quantity_damaged': 0
                    }],
                    submission_key=f'demo-receipt-{po_number:05d}',
                    user=manager
                )

                received_at = self.timestamp(last_day, 18)

                GoodsReceipt.objects.filter(pk=receipt.pk).update(
                    received_at=received_at
                )

                StockMovement.objects.filter(
                    movement_type='receipt',
                    source=receipt.receipt_number
                ).update(created_at=received_at)

                receipt_count += 1

        return po_number, receipt_count
```

**NEW — `inventory/test_seed_demo_data.py`** (complete committed source):

```python
from io import StringIO
from unittest.mock import patch

from django.contrib.auth import get_user_model
from django.core.management import call_command
from django.core.management.base import CommandError
from django.test import TestCase

from catalog.models import Product, Supplier
from inventory.models import StockMovement
from purchasing.models import GoodsReceipt, PurchaseOrder
from sales.models import Sale


class SeedDemoDataCommandTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='seed_manager',
            email='seed_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='seed_sales',
            email='seed_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

    def test_manager_argument_is_required(self):
        with self.assertRaisesMessage(
            CommandError, 'Provide --manager'
        ):
            call_command(
                'seed_demo_data',
                stdout=StringIO()
            )

        self.assertEqual(Supplier.objects.count(), 0)

    def test_sales_staff_cannot_run_seed(self):
        with self.assertRaisesMessage(
            CommandError, 'active Inventory Manager'
        ):
            call_command(
                'seed_demo_data',
                manager=self.sales_user.username,
                stdout=StringIO()
            )

        self.assertFalse(Product.objects.exists())

    def test_inactive_manager_cannot_run_seed(self):
        self.manager.is_active = False
        self.manager.save(update_fields=['is_active'])

        with self.assertRaisesMessage(
            CommandError, 'active Inventory Manager'
        ):
            call_command(
                'seed_demo_data',
                manager=self.manager.username,
                stdout=StringIO()
            )

    def test_existing_sales_block_seed(self):
        Sale.objects.create(
            sale_number='EXISTING-001',
            submission_key='existing-sale',
            created_by=self.sales_user
        )

        with self.assertRaisesMessage(
            CommandError, 'Sales already exist'
        ):
            call_command(
                'seed_demo_data',
                manager=self.manager.username,
                stdout=StringIO()
            )

        self.assertEqual(Sale.objects.count(), 1)
        self.assertFalse(Supplier.objects.exists())

    def test_existing_demo_sku_blocks_seed(self):
        supplier = Supplier.objects.create(
            name='Other Supplier'
        )

        Product.objects.create(
            sku='DEMO-001',
            name='Existing Product',
            category='Existing',
            supplier=supplier,
            unit_price='10.00'
        )

        with self.assertRaisesMessage(
            CommandError, 'Demo product SKUs already exist'
        ):
            call_command(
                'seed_demo_data',
                manager=self.manager.username,
                stdout=StringIO()
            )

        self.assertEqual(Product.objects.count(), 1)
        self.assertEqual(Supplier.objects.count(), 1)

    @patch(
        'inventory.management.commands.seed_demo_data.record_sale',
        side_effect=ValueError('Simulated sale failure')
    )
    def test_failure_rolls_back_everything(self, mock_sale):
        with self.assertRaisesMessage(
            CommandError, 'Simulated sale failure'
        ):
            call_command(
                'seed_demo_data',
                manager=self.manager.username,
                stdout=StringIO()
            )

        mock_sale.assert_called_once()

        self.assertFalse(Supplier.objects.exists())
        self.assertFalse(Product.objects.exists())
        self.assertFalse(PurchaseOrder.objects.exists())
        self.assertFalse(GoodsReceipt.objects.exists())
        self.assertFalse(StockMovement.objects.exists())
        self.assertFalse(Sale.objects.exists())
```

### Code walkthrough

1. **Command location and arguments.** Django discovers `seed_demo_data` inside `inventory/management/commands/`. Its `--manager` option requires an existing, active Manager; the current eight user accounts are not changed or recreated.
2. **Safety guardrails.** Before any writes, the command refuses to run with existing sales, pre-existing `DEMO-` SKUs or pre-existing `DEMO-PO-` numbers. These checks prevent accidentally mixing multiple seeded datasets. All writes run inside `transaction.atomic()` so exceptions roll back inserts.
3. **Supplier and product setup.** Three suppliers, five category labels, and 25 products are created. `change_stock(... movement_type='opening')` inserts initial stock into the authoritative movement ledger. The function `timestamp()` supplies a timezone-aware simulated date.
4. **Daily demand.** A `random.Random(2026)` instance controls the selected products, sale counts and quantities. The loop iterates over exactly 365 dates. Weekdays, weekends and six anomaly-day indices have different sale-volume rules.
5. **Stock preparation and purchasing.** Each day first plans its sales and adds up demand per product, then creates purchase orders if inventory is low or a weekly restock is due. It uses the existing `create_purchase_order`, `change_purchase_order_status` and `receive_goods` services. Every receipt updates the stock ledger through the normal service.
6. **Sales and history.** `record_sale` creates normal Sale and SaleLine rows and negative stock movements; discount values follow the existing supported options. Historical timestamps are updated after service calls to make the simulated year visible in date-filtered dashboards.
7. **Open purchase-order examples.** Three last-day purchase orders exercise `ordered` and `partially_received` states, with past and future expected delivery dates.
8. **Integrity check.** Before committing, the command calls the pre-existing `check_ledger` command, which compares each product's balances and stock movement arithmetic.
9. **Tests.** Six `TestCase` methods verify the required Manager argument, rejection of Sales users and inactive Managers, refusal when sales or demo SKUs already exist, and rollback when a mocked `record_sale` fails. These are safety tests, not yet a full simulated-year integration test.

### How we checked it

The user ran the new seed command test module and existing stock regression tests; the supplied terminal log confirms **30 tests passed in 19.268 seconds**, followed by `manage.py check` reporting no issues and `makemigrations --check --dry-run` reporting no changes. Both new files passed staged-whitespace checking and were committed and pushed to source `main` in **`ab5d7ce`**. I fetched and checked the committed files and counted **six new test methods**.

**No real seed command was run against the user's cleared working database yet.** The full 365-day generation and the complete Django regression suite will be verified in Chapter 9. The last observed full-project test run was **336 tests after Chapter 5**; Chapters 6–8 added 12 + 13 + 6 test methods, giving **367 expected** full-suite tests, pending verification.

### Next

Previous: [Step 378](07-administrator-dashboard.md#step-378). Next: Module 6 Chapter 9 — seed integration tests, chronological ledger verification and the full Django regression suite. Only after validation should the user populate their local database.

## Chapter summary

The project has a local data generator that observes normal service-layer rules and supports an annual demo dataset. Current verification covers refusal/rollback cases and existing inventory tests. Full-year data correctness remains the next chapter's task.

**Recall question:** Why does the command use `record_sale` and `receive_goods` instead of directly creating sale and receipt rows?

**Source commit:** [`ab5d7ce`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/ab5d7ce).
