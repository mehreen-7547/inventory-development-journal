# Post-Core Data Maintenance — Realistic Branded Demo Dataset

## Before this chapter

Module 6 was completed and marked with the immutable `m6-complete` source tag, after which the user inspected the Manager dashboard with the first 365-day demo dataset. The data was technically consistent, but it was unconvincing: 25 generic products, repetitive sale sizes, repeated restocks and six very obvious artificial spikes. The user requested real brand names, richer transactions and more plausible seasonality before beginning the AI demand-intelligence Stretch work.

This is a **post-Core maintenance change**, not a reopening of Module 6. It changes the data generator, not Django models, and does not update the `m6-complete` tag.

## What we did

A new Django management command generates a 365-day, synthetic **48-product branded catalog**, supplied by **five fictional wholesalers**, with varying demand weights and approximate illustrative INR product prices. It produces seasonally varied daily sales, discount offers, occasional cancellations/stock reversals, occasional physical-count adjustments, supplier-specific lead times, delayed and split receipts, and representative open purchase orders. All persistent inventory, sales and PO actions use existing business services.

The command intentionally **refuses to run if any business records exist**, so it cannot be accidentally run on top of the earlier 1,085-sale seed. A replacement of the local database must be a **separate, backed-up, explicitly confirmed** step. It never creates or deletes user accounts.

## Step 382 — Add and verify realistic historical data generation

### Before this step

The original `seed_demo_data` remains in the repository for reproducibility and its 374 existing tests. The new command has a different name, `seed_realistic_data`, so there is no risk of silently changing or breaking those older tests.

### What we did and why

The application does not have a separate Product brand field, so catalog entries include recognizable brand, pack size and model directly in the existing `Product.name` field. Each existing Product still has exactly **one supplier**. Demand diversity is generated procedurally through a fixed random seed, product/category weights, weekday differences, seasonal multipliers and irregular campaign days. Transactions go through `record_sale`, `void_sale`, `change_stock`, `create_purchase_order`, `change_purchase_order_status`, and `receive_goods`, rather than bypassing ledger checks by inserting all records manually.

### Code

**NEW — `inventory/management/commands/seed_realistic_data.py`** (complete exact source verified at commit `5c62595`):

```python
"""Create a realistic, entirely synthetic one-year retail inventory history.

All brand names identify example catalog products; vendors and transactions are
fictional. Run only after integration tests and a separate, backed-up reset.
"""

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
from sales.services import record_sale, void_sale


# Category, branded item and pack size, approximate illustrative INR price,
# relative demand weight. These are NOT verified retail prices or real sales.
CATALOG = [
    ('Stationery', 'Classmate Pulse Notebook A4 180 Pages', '115.00', 5.2),
    ('Stationery', 'Classmate Long Notebook 172 Pages', '85.00', 5.5),
    ('Stationery', 'Navneet Youva Notebook A5 160 Pages', '75.00', 3.4),
    ('Stationery', 'Cello Butterflow Blue Ball Pen Pack of 10', '100.00', 4.8),
    ('Stationery', 'Reynolds 045 Fine Carbure Pen Pack of 5', '50.00', 3.8),
    ('Stationery', 'Pilot V5 Hi-Tecpoint Blue Pen', '65.00', 2.6),
    ('Stationery', 'Faber-Castell HB Pencils Pack of 10', '70.00', 3.5),
    ('Stationery', 'Camlin Kokuyo Exam Eraser Pack of 5', '50.00', 2.7),
    ('Stationery', 'DOMS Geometry Box', '145.00', 1.5),
    ('Stationery', 'Apsara Platinum Pencils Pack of 10', '65.00', 4.3),

    ('Office Supplies', 'JK Copier A4 Paper 75 GSM 500 Sheets', '345.00', 4.8),
    ('Office Supplies', 'Century Star A4 Paper 70 GSM 500 Sheets', '285.00', 4.2),
    ('Office Supplies', 'Kangaro HD-10D Stapler', '170.00', 2.0),
    ('Office Supplies', 'Kangaro Staple Pins No.10 Pack of 10', '135.00', 3.5),
    ('Office Supplies', 'Solo A4 Document File 20 Pockets', '145.00', 2.8),
    ('Office Supplies', 'Post-it Notes 3x3 Inches 100 Sheets', '115.00', 2.7),
    ('Office Supplies', 'Oddy Transparent Tape 1 Inch', '35.00', 3.2),
    ('Office Supplies', 'Artline 70 Permanent Marker Black', '45.00', 3.1),
    ('Office Supplies', 'Kores Glue Stick 15g', '55.00', 2.5),
    ('Office Supplies', 'Casio MX-12B Desktop Calculator', '495.00', 0.9),

    ('Electronics', 'Logitech M171 Wireless Mouse', '695.00', 1.4),
    ('Electronics', 'Dell MS116 USB Optical Mouse', '449.00', 1.8),
    ('Electronics', 'Logitech K120 Wired Keyboard', '795.00', 0.8),
    ('Electronics', 'SanDisk Ultra Flair USB 3.0 32GB', '525.00', 1.3),
    ('Electronics', 'SanDisk Cruzer Blade USB 2.0 64GB', '625.00', 0.9),
    ('Electronics', 'Portronics Konnect USB-C Cable 1m', '199.00', 1.7),
    ('Electronics', 'boAt Type-C USB Cable 1m', '229.00', 1.5),
    ('Electronics', 'Duracell AA Alkaline Batteries Pack of 4', '185.00', 2.7),
    ('Electronics', 'TP-Link UH400 USB 3.0 Hub 4 Port', '895.00', 0.6),

    ('Cleaning', 'Dettol Original Liquid Handwash 200ml', '99.00', 4.8),
    ('Cleaning', 'Savlon Moisture Shield Handwash 200ml', '95.00', 3.5),
    ('Cleaning', 'Lizol Citrus Floor Cleaner 500ml', '119.00', 4.4),
    ('Cleaning', 'Harpic Original Toilet Cleaner 500ml', '115.00', 3.9),
    ('Cleaning', 'Colin Glass Cleaner 500ml', '115.00', 2.7),
    ('Cleaning', 'Vim Dishwash Liquid 500ml', '125.00', 3.6),
    ('Cleaning', 'Surf Excel Easy Wash Detergent 1kg', '135.00', 3.2),
    ('Cleaning', 'Scotch-Brite Scrub Pad Pack of 3', '95.00', 2.5),
    ('Cleaning', 'Good Knight Mosquito Repellent Refill 45ml', '89.00', 3.0),

    ('Pantry', 'Tata Tea Premium Tea 250g', '165.00', 4.8),
    ('Pantry', 'Brooke Bond Red Label Tea 250g', '155.00', 4.3),
    ('Pantry', 'Nescafe Classic Instant Coffee 50g', '205.00', 3.2),
    ('Pantry', 'Bru Instant Coffee 50g', '195.00', 2.5),
    ('Pantry', 'Parle-G Original Biscuits 800g', '105.00', 5.0),
    ('Pantry', 'Britannia Good Day Butter Cookies 200g', '45.00', 4.7),
    ('Pantry', 'Britannia Marie Gold Biscuits 250g', '40.00', 4.0),
    ('Pantry', 'Dhampur White Sugar 1kg', '62.00', 3.7),
    ('Pantry', 'Bisleri Packaged Drinking Water 1L', '20.00', 5.2),
    ('Pantry', 'Paper Boat Aamras Drink 200ml', '35.00', 2.1),
]

SUPPLIERS = [
    ('Coastal Stationery Distribution', 'Stationery', 4),
    ('Malabar Office Trade', 'Office Supplies', 5),
    ('Harbour Tech Distributors', 'Electronics', 8),
    ('Kerala Homecare Wholesale', 'Cleaning', 3),
    ('Backwater FMCG Supply', 'Pantry', 2),
]

# Sales campaigns and market patterns are invented, not factual holidays.
LOW_TRADE_DATES = {(1, 1), (8, 15), (10, 2), (12, 25)}


class Command(BaseCommand):
    help = 'Create branded, varied synthetic inventory history in an EMPTY business database'

    def add_arguments(self, parser):
        parser.add_argument(
            '--manager',
            required=True,
            help='Existing active Inventory Manager username'
        )

    def timestamp(self, day, hour, minute=0):
        return timezone.make_aware(datetime.combine(day, time(hour, minute)))

    def handle(self, *args, **options):
        manager = get_user_model().objects.filter(
            username=options['manager'],
            role='manager',
            is_active=True
        ).first()

        if manager is None:
            raise CommandError('An existing active Manager is required.')

        # Never mix this with earlier seeded records or a real business.
        if (
            Supplier.objects.exists()
            or Product.objects.exists()
            or PurchaseOrder.objects.exists()
            or GoodsReceipt.objects.exists()
            or StockMovement.objects.exists()
            or Sale.objects.exists()
        ):
            raise CommandError(
                'Business records already exist. Nothing changed. '
                'Only use this on an empty, backed-up business database.'
            )

        rng = random.Random(20261112)
        today = timezone.localdate()
        first_day = today - timedelta(days=364)
        sales_users = list(
            get_user_model().objects.filter(
                role='sales', is_active=True
            ).order_by('pk')
        ) or [manager]

        try:
            with transaction.atomic():
                vendors = self.create_suppliers()
                products, available = self.create_products(
                    vendors, manager, first_day, rng
                )

                on_order = defaultdict(int)
                delivery_queue = []
                counts = defaultdict(int)
                po_sequence = 0
                campaign_days = self.pick_campaign_days(rng, first_day)

                for offset in range(365):
                    day = first_day + timedelta(days=offset)
                    current_hour = timezone.localtime().hour

                    if day < today or current_hour >= 9:
                        self.process_deliveries(
                            day, delivery_queue, available, on_order, manager
                        )

                    planned_count = self.sales_for_day(day, rng, campaign_days)

                    if day == today:
                        # Do not put future-dated transactions into the database.
                        planned_count = min(planned_count, max(0, current_hour - 9))

                    for position in range(planned_count):
                        items = self.pick_sale_items(
                            products, available, day, rng
                        )

                        if not items:
                            continue

                        seller = sales_users[rng.randrange(len(sales_users))]
                        if day == today:
                            now_local = timezone.localtime()
                            end_minute = max(
                                9 * 60,
                                now_local.hour * 60 + now_local.minute - 15
                            )
                            start_minute = 9 * 60
                        else:
                            start_minute = 10 * 60
                            end_minute = 18 * 60 + 30

                        minute_of_day = start_minute + (
                            (position + 1) * (end_minute - start_minute)
                            // (planned_count + 1)
                        )
                        sold_at = self.timestamp(
                            day, minute_of_day // 60, minute_of_day % 60
                        )
                        roll = rng.random()
                        discount = 10 if roll < 0.035 else 5 if roll < 0.12 else 0

                        sale = record_sale(
                            items=items,
                            user=seller,
                            submission_key=f'real-{offset:03d}-{position:02d}',
                            discount_type='percent' if discount else 'none',
                            discount_value=discount
                        )
                        Sale.objects.filter(pk=sale.pk).update(created_at=sold_at)
                        StockMovement.objects.filter(
                            movement_type='sale', source=sale.sale_number
                        ).update(created_at=sold_at)

                        for item in items:
                            available[item['product_id']] -= item['quantity']

                        counts['sales'] += 1

                        # Genuine cancellation/reversal through the existing service.
                        if rng.random() < 0.012:
                            void_sale(sale.pk, 'Customer cancelled order', manager)
                            voided_at = sold_at + timedelta(minutes=5)
                            Sale.objects.filter(pk=sale.pk).update(voided_at=voided_at)
                            StockMovement.objects.filter(
                                movement_type='void', source=sale.sale_number
                            ).update(created_at=voided_at)
                            for item in items:
                                available[item['product_id']] += item['quantity']
                            counts['voided'] += 1

                    # Small occasional differences found during stock counting.
                    if offset % 37 == 0 and day < today:
                        candidate = rng.choice(products)
                        current = available[candidate.pk]
                        difference = -1 if current > 2 else 1
                        movement = change_stock(
                            product_id=candidate.pk,
                            delta=difference,
                            movement_type='adjustment',
                            reason='Periodic physical cycle count',
                            user=manager,
                            expected_balance=current
                        )
                        StockMovement.objects.filter(pk=movement.pk).update(
                            created_at=self.timestamp(day, 20)
                        )
                        available[candidate.pk] += difference
                        counts['adjustments'] += 1

                    if day < today or current_hour >= 19:
                        po_sequence += self.order_replenishment(
                            day, products, vendors, available, on_order,
                            delivery_queue, manager, rng, po_sequence
                        )

                # Deliberately leave a few useful, varied open PO examples.
                self.create_open_orders(products, vendors, manager, today, po_sequence)
                call_command('check_ledger', stdout=self.stdout)

                self.stdout.write(self.style.SUCCESS(
                    f'Realistic seed complete: {counts["sales"]} sales '
                    f'({counts["voided"]} voided) from {first_day} to {today}.'
                ))
                self.stdout.write(
                    f'{len(products)} branded products, {len(vendors)} '
                    f'fictional suppliers, {PurchaseOrder.objects.count()} '
                    f'purchase orders, {GoodsReceipt.objects.count()} receipts.'
                )
                self.stdout.write(
                    f'{counts["adjustments"]} stock counts; '
                    f'{len(campaign_days)} synthetic campaign days.'
                )

        except ValueError as error:
            raise CommandError(str(error)) from error

    def create_suppliers(self):
        return {
            category: (Supplier.objects.create(
                name=name,
                address='Kochi, Kerala — fictional demonstration wholesaler'
            ), lead_days)
            for name, category, lead_days in SUPPLIERS
        }

    def create_products(self, vendors, manager, first_day, rng):
        products = []
        available = {}

        for number, (category, name, price, weight) in enumerate(CATALOG, 1):
            supplier, lead_days = vendors[category]
            product = Product.objects.create(
                sku=f'BRD-{number:03d}',
                name=name,
                category=category,
                supplier=supplier,
                unit_price=Decimal(price),
                safety_stock=int(12 + weight * 3),
                lead_time_days=lead_days
            )
            opening = int(24 + weight * 9 + rng.randint(-8, 15))
            movement = change_stock(
                product_id=product.pk,
                delta=opening,
                movement_type='opening',
                reason='Opening stock for synthetic historical dataset',
                user=manager,
                source=f'brand-opening-{number:03d}'
            )
            StockMovement.objects.filter(pk=movement.pk).update(
                created_at=self.timestamp(first_day, 8)
            )
            product.demand_weight = weight
            products.append(product)
            available[product.pk] = opening

        return products, available

    def pick_campaign_days(self, rng, first_day):
        candidates = [
            first_day + timedelta(days=i)
            for i in range(15, 350)
            if (
                (first_day + timedelta(days=i)).month,
                (first_day + timedelta(days=i)).day
            ) not in LOW_TRADE_DATES
        ]
        return set(rng.sample(candidates, 9))

    def sales_for_day(self, day, rng, campaigns):
        weekday_base = [7, 7, 8, 8, 10, 12, 4][day.weekday()]
        count = max(0, weekday_base + rng.randint(-3, 4))

        if (day.month, day.day) in LOW_TRADE_DATES:
            count = rng.choice([0, 1, 2])
        elif day in campaigns:
            count = int(count * rng.uniform(1.5, 2.3)) + 2
        elif day.month in (5, 6, 11, 12) and rng.random() < 0.24:
            count += rng.randint(2, 6)

        return count

    def pick_sale_items(self, products, available, day, rng):
        desired_lines = rng.choices([1, 2, 3, 4], [45, 35, 16, 4])[0]
        selected = set()
        items = []

        for unused in range(desired_lines):
            choices = [
                p for p in products
                if available[p.pk] > 0 and p.pk not in selected
            ]
            if not choices:
                break

            weights = []
            for product in choices:
                multiplier = 1.0
                if product.category == 'Stationery' and day.month in (5, 6):
                    multiplier *= 1.9
                elif product.category == 'Office Supplies' and day.month in (3, 4):
                    multiplier *= 1.5
                elif product.category == 'Cleaning' and day.month in (6, 7, 8):
                    multiplier *= 1.35
                elif product.category == 'Pantry' and day.month in (11, 12):
                    multiplier *= 1.3
                weights.append(product.demand_weight * multiplier)

            product = rng.choices(choices, weights=weights)[0]
            quantity = rng.choices([1, 2, 3, 4], [60, 23, 12, 5])[0]
            if rng.random() < 0.028:
                quantity += rng.randint(4, 10)
            quantity = min(quantity, available[product.pk])

            selected.add(product.pk)
            items.append({'product_id': product.pk, 'quantity': quantity})

        return items

    def order_replenishment(
        self, day, products, vendors, available, on_order,
        delivery_queue, manager, rng, po_sequence
    ):
        by_supplier = defaultdict(list)

        for product in products:
            stock_position = available[product.pk] + on_order[product.pk]
            trigger = product.safety_stock + int(product.demand_weight * 5)
            if stock_position > trigger:
                continue

            target = int(55 + product.demand_weight * 20)
            needed = target - stock_position
            if needed <= 0:
                continue

            by_supplier[product.supplier_id].append({
                'product_id': product.pk,
                'quantity': needed,
                'unit_cost': str(
                    (product.unit_price * Decimal('0.72')).quantize(
                        Decimal('0.01')
                    )
                )
            })

        created = 0
        for supplier_id, lines in by_supplier.items():
            po_sequence += 1
            created += 1
            po_number = f'RPO-{po_sequence:05d}'
            order = create_purchase_order(
                po_number=po_number,
                supplier_id=supplier_id,
                items=lines,
                user=manager
            )
            change_purchase_order_status(order.pk, 'ordered', manager)
            lead_days = next(
                lead for supplier, lead in vendors.values()
                if supplier.pk == supplier_id
            )
            expected = day + timedelta(days=lead_days)
            PurchaseOrder.objects.filter(pk=order.pk).update(
                created_at=self.timestamp(day, 19),
                expected_delivery_date=expected
            )

            arrival = expected + timedelta(days=rng.choices(
                [-1, 0, 1, 2, 4], [5, 53, 22, 14, 6]
            )[0])
            arrival = max(day + timedelta(days=1), arrival)

            all_quantities = {item['product_id']: item['quantity'] for item in lines}
            for pk, qty in all_quantities.items():
                on_order[pk] += qty

            if rng.random() < 0.26:
                first = {pk: max(1, int(qty * 0.62)) for pk, qty in all_quantities.items()}
                second = {pk: all_quantities[pk] - first[pk] for pk in all_quantities}
                delivery_queue.append((arrival, order.pk, first))
                rest = {pk: qty for pk, qty in second.items() if qty > 0}
                if rest:
                    delivery_queue.append((
                        arrival + timedelta(days=rng.randint(1, 5)), order.pk, rest
                    ))
            else:
                delivery_queue.append((arrival, order.pk, all_quantities))

        return created

    def process_deliveries(self, day, queue, available, on_order, manager):
        delivered = 0
        remaining = []

        for arrival, order_id, quantities in queue:
            if arrival > day:
                remaining.append((arrival, order_id, quantities))
                continue

            # One occasional unit can be damaged, with no stock increase.
            items = []
            for product_id, amount in quantities.items():
                damaged = 1 if amount >= 20 and (order_id + product_id) % 23 == 0 else 0
                items.append({
                    'product_id': product_id,
                    'quantity_received': amount - damaged,
                    'quantity_damaged': damaged
                })

            receipt = receive_goods(
                order_id=order_id,
                items=items,
                submission_key=f'brand-rec-{order_id}-{delivered}-{day}',
                user=manager
            )
            GoodsReceipt.objects.filter(pk=receipt.pk).update(
                received_at=self.timestamp(day, 9)
            )
            StockMovement.objects.filter(
                movement_type='receipt', source=receipt.receipt_number
            ).update(created_at=self.timestamp(day, 9))

            for item in items:
                pk = item['product_id']
                total = item['quantity_received'] + item['quantity_damaged']
                available[pk] += item['quantity_received']
                on_order[pk] -= total
                if on_order[pk] < 0:
                    raise ValueError('Internal on-order tracking became negative.')
            delivered += 1

        queue[:] = remaining
        return delivered

    def create_open_orders(self, products, vendors, manager, today, last_number):
        examples = [
            ('Office Supplies', today - timedelta(days=5), False),
            ('Electronics', today + timedelta(days=5), False),
            ('Cleaning', today - timedelta(days=2), True),
        ]
        for offset, (category, expected, partial) in enumerate(examples, 1):
            supplier, _ = vendors[category]
            product = next(p for p in products if p.category == category)
            order = create_purchase_order(
                po_number=f'RPO-{last_number + offset:05d}',
                supplier_id=supplier.pk,
                items=[{
                    'product_id': product.pk,
                    'quantity': 80,
                    'unit_cost': str(
                        (product.unit_price * Decimal('0.72')).quantize(
                            Decimal('0.01')
                        )
                    )
                }],
                user=manager
            )
            change_purchase_order_status(order.pk, 'ordered', manager)
            PurchaseOrder.objects.filter(pk=order.pk).update(
                created_at=self.timestamp(today - timedelta(days=12), 19),
                expected_delivery_date=expected
            )

            if partial:
                receive_goods(
                    order_id=order.pk,
                    items=[{
                        'product_id': product.pk,
                        'quantity_received': 20,
                        'quantity_damaged': 0
                    }],
                    submission_key=f'brand-partial-{last_number + offset}',
                    user=manager
                )
```

**NEW — `inventory/test_realistic_seed.py`** (complete exact source verified at commit `5c62595`):

```python
from datetime import timedelta
from io import StringIO
from unittest.mock import patch

from django.contrib.auth import get_user_model
from django.core.management import call_command
from django.core.management.base import CommandError
from django.db.models import Count
from django.db.models.functions import TruncDate
from django.test import TestCase
from django.utils import timezone

from catalog.models import Product, Supplier
from inventory.management.commands.seed_realistic_data import CATALOG
from inventory.models import StockMovement
from purchasing.models import GoodsReceipt, PurchaseOrder
from sales.models import Sale, SaleLine


class RealisticSeedSafetyTests(TestCase):

    @classmethod
    def setUpTestData(cls):
        User = get_user_model()
        cls.manager = User.objects.create_user(
            username='realistic_manager',
            email='realistic_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )
        cls.sales = User.objects.create_user(
            username='realistic_sales',
            email='realistic_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

    def test_active_manager_is_required(self):
        with self.assertRaisesMessage(CommandError, 'active Manager'):
            call_command(
                'seed_realistic_data', manager=self.sales.username,
                stdout=StringIO()
            )
        self.assertFalse(Product.objects.exists())

    def test_existing_business_data_is_never_modified(self):
        Supplier.objects.create(name='Existing Supplier')
        with self.assertRaisesMessage(CommandError, 'Business records already exist'):
            call_command(
                'seed_realistic_data', manager=self.manager.username,
                stdout=StringIO()
            )
        self.assertEqual(Supplier.objects.count(), 1)
        self.assertFalse(Product.objects.exists())

    @patch(
        'inventory.management.commands.seed_realistic_data.record_sale',
        side_effect=ValueError('Simulated failure')
    )
    def test_generator_failure_rolls_back_everything(self, mock_sale):
        with self.assertRaisesMessage(CommandError, 'Simulated failure'):
            call_command(
                'seed_realistic_data', manager=self.manager.username,
                stdout=StringIO()
            )
        mock_sale.assert_called()
        self.assertFalse(Supplier.objects.exists())
        self.assertFalse(Product.objects.exists())
        self.assertFalse(PurchaseOrder.objects.exists())
        self.assertFalse(GoodsReceipt.objects.exists())
        self.assertFalse(Sale.objects.exists())
        self.assertFalse(StockMovement.objects.exists())


class RealisticSeedIntegrationTests(TestCase):

    @classmethod
    def setUpTestData(cls):
        User = get_user_model()
        cls.manager = User.objects.create_user(
            username='realistic_integration_manager',
            email='realistic_integration_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )
        cls.sales = User.objects.create_user(
            username='realistic_integration_sales',
            email='realistic_integration_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )
        cls.today = timezone.localdate()
        cls.output = StringIO()
        call_command(
            'seed_realistic_data',
            manager=cls.manager.username,
            stdout=cls.output
        )

    def test_branded_catalog_and_supplier_links(self):
        self.assertEqual(Product.objects.count(), len(CATALOG))
        self.assertEqual(len(CATALOG), 48)
        self.assertEqual(Supplier.objects.count(), 5)
        self.assertEqual(
            len(set(Product.objects.values_list('category', flat=True))),
            5
        )
        self.assertTrue(Product.objects.filter(name__icontains='Classmate').exists())
        self.assertTrue(Product.objects.filter(name__icontains='Logitech').exists())
        self.assertTrue(Product.objects.filter(name__icontains='Dettol').exists())
        self.assertTrue(Product.objects.filter(name__icontains='Nescafe').exists())
        self.assertTrue(Product.objects.filter(name__icontains='JK Copier').exists())
        self.assertEqual(
            StockMovement.objects.filter(movement_type='opening').count(),
            48
        )
        for product in Product.objects.select_related('supplier'):
            self.assertGreater(product.on_hand, -1)
            self.assertEqual(product.supplier.is_active, True)

    def test_one_year_of_varied_sales_including_voids(self):
        self.assertGreater(Sale.objects.count(), 1600)
        self.assertLess(Sale.objects.count(), 5500)
        self.assertTrue(Sale.objects.filter(status='voided').exists())
        self.assertTrue(Sale.objects.filter(discount_type='percent').exists())
        self.assertGreater(SaleLine.objects.count(), Sale.objects.count())

        days = list(
            Sale.objects.annotate(
                day=TruncDate('created_at')
            ).values('day').annotate(
                count=Count('id')
            )
        )
        self.assertGreater(len(days), 340)
        self.assertGreater(len({x['count'] for x in days}), 8)
        earliest = Sale.objects.order_by('created_at').first().created_at.date()
        self.assertGreaterEqual(earliest, self.today - timedelta(days=364))
        self.assertFalse(
            Sale.objects.filter(created_at__date__gt=self.today).exists()
        )

    def test_orders_receipts_and_partial_delivery(self):
        self.assertGreater(PurchaseOrder.objects.count(), 12)
        self.assertGreater(GoodsReceipt.objects.count(), 8)
        self.assertTrue(
            PurchaseOrder.objects.filter(status='ordered').exists()
        )
        self.assertTrue(
            PurchaseOrder.objects.filter(status='partially_received').exists()
        )
        self.assertTrue(
            PurchaseOrder.objects.filter(status='received').exists()
        )
        for order in PurchaseOrder.objects.prefetch_related('lines'):
            for line in order.lines.all():
                self.assertLessEqual(line.quantity_received, line.quantity_ordered)

    def test_every_stock_balance_reconciles(self):
        output = StringIO()
        call_command('check_ledger', stdout=output)
        self.assertIn('Stock ledger check passed', output.getvalue())
        self.assertEqual(
            StockMovement.objects.filter(movement_type='sale').count(),
            SaleLine.objects.count()
        )
        self.assertTrue(
            StockMovement.objects.filter(movement_type='void').exists()
        )
        self.assertTrue(
            StockMovement.objects.filter(movement_type='adjustment').exists()
        )

    def test_repeat_is_rejected_and_users_preserved(self):
        users_before = get_user_model().objects.count()
        sales_before = Sale.objects.count()
        with self.assertRaisesMessage(CommandError, 'Business records already exist'):
            call_command(
                'seed_realistic_data', manager=self.manager.username,
                stdout=StringIO()
            )
        self.assertEqual(get_user_model().objects.count(), users_before)
        self.assertEqual(Sale.objects.count(), sales_before)
```

### Code walkthrough

1. **Catalog and supplier definitions:** `CATALOG` includes branded stationery, office supplies, electronics, cleaning and pantry products with synthetic INR sale prices and per-product demand weights. `SUPPLIERS` maps each category to one fictional wholesaler and lead time, matching the approved one-supplier-per-product scope.
2. **Refusal before writing:** `handle` looks up an existing active Manager and checks all business models for records. It aborts with `CommandError` if anything already exists; the code never deletes data on its own. All creation is wrapped in a single `transaction.atomic()` block, and `check_ledger` runs before commit.
3. **Opening inventory:** `create_products` creates 48 branded items, realistic but approximate price labels, different safety-stock levels and initial quantities. Existing `change_stock` records the opening balances and preserves a chain of `StockMovement` entries.
4. **Demand behavior:** `sales_for_day` uses varying weekday baselines, noise, a handful of low-trade dates, nine randomly picked synthetic campaign dates and periodic higher category-season demand. `pick_sale_items` chooses product mixes and quantities by relative popularity, seasonal relevance and available stock.
5. **Transactional realism:** All sales use `record_sale`. Some transactions have 5% or 10% discounts; occasional cancellations use `void_sale` to add legitimate stock reversals. Physical counts use `change_stock` with a stock-count reason and a before/after ledger change.
6. **Purchasing and receipts:** `order_replenishment` uses stock position (`on_hand + on_order`) instead of only on-hand stock and schedules purchases by supplier. Deliveries can be early/late or split into two receipts. `process_deliveries` calls `receive_goods`, handles occasional damaged quantity and reconciles `on_order`.
7. **Open purchase orders:** `create_open_orders` creates readable examples of overdue, future-dated and partially received purchase orders, so the existing Manager dashboard has meaningful purchase-order attention data.
8. **History and timestamps:** The simulation spans 365 days ending on the current day, with current-day sales capped to avoid future-dated transactions. Related sale, void, receipt and movement timestamps are adjusted after calls to existing services.
9. **Automated verification:** Three safety tests confirm Manager authorization, refusal to overwrite business data and rollback after a simulated sale failure. Five integration tests generate the full year in a temporary Django test database and validate branded catalog counts, demand variation, stock movements, purchasing states, ledger reconciliation, repeat-run refusal and preservation of test accounts.

### How we checked it

The user first placed the test file inside `inventory/management/commands`, which prevented Django from importing `inventory.test_realistic_seed`. After moving the test file directly into `inventory/`, discovery correctly found eight tests.

The user supplied the following passing results:

- Focused test module: **8 tests passed in 19.329 seconds**.
- Full project regression: **398 tests passed in 1,084.055 seconds** (previous 390 plus eight new tests).
- `python manage.py check_ledger`: **Stock ledger check passed** on the user's existing populated database.
- `python manage.py check`: **No system-check issues**.
- `python manage.py makemigrations --check --dry-run`: **No changes detected**.
- Two files created and pushed to source `main` in commit **`5c62595`**. I fetched and verified both committed files.

The full test suite used Django's **temporary test database**. At this checkpoint, the user's actual SQLite database still contains the *old* 25-product, 1,085-sale data. The newer branded dataset has not yet been inserted there.

### Next — replace local demo business data, preserving accounts

From the project directory containing `manage.py`, use the previously verified `reset_business_data_once.py` (an independently supplied script, **not** part of this commit) only after confirming the current database and user count. It requires typing the exact confirmation `RESET BUSINESS DATA`, makes a separate SQLite backup via `sqlite3.Connection.backup`, then deletes only the existing Sale/SaleLine, GoodsReceipt/GoodsReceiptLine, PurchaseOrder/PurchaseOrderLine, StockMovement, Product and Supplier tables' records. It checks that the user count is unchanged. The backup must be confirmed before running the new generator.

Run `python manage.py seed_realistic_data --manager manager1` only once the reset script reports success and confirms the business tables are empty. Then run `python manage.py check_ledger` and inspect the dashboard. If generation fails, **do not repeat reset**; preserve the backup and investigate.

**Do not delete any existing user accounts; do not rerun the original `seed_demo_data` command.**

### Scope boundaries and limitations

The records are *synthetic and illustrative*, not brand-sourced sales data, actual wholesale contracts, real pricing quotations, official seasonal patterns or validated demand forecasts. Brand names do not imply endorsement or product pricing accuracy. Their purpose is to give the academic Django system a richer, internally consistent development/demo history for later AI investigation. The saved `m6-complete` tag remains at source commit `5ce9647`.

## Chapter summary

Post-Core Step 382 introduced and verified a second, more believable data generator in source commit `5c62595`. The full suite passed all 398 tests; replacement of the user's working dataset is the next manual step, with a backup and preservation of eight existing accounts.

**Recall question:** Why must the realistic seed command refuse to run while business records exist, rather than automatically deleting the old sales and purchase orders?

**Source commit:** [`5c62595`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5c62595).
