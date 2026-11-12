# Module 6 — Chapter 1: Manager Dashboard Summary Cards

## Before we begin
Modules 0–5 provided the underlying sales, product and purchase-order workflows. The home page already offered role-specific navigation, but no calculated Manager statistics. This chapter introduces only the six approved summary cards, without adding database fields, apps or chart libraries.

## What we worked on
- A plain query function in `core/dashboard.py`.
- A Manager-only context value in the existing home view.
- Six Bootstrap cards in the existing home template.
- Five focused tests and a full-project regression run.

## Picture it this way
The dashboard is a window into existing records, not a second database. Opening the Manager home page calculates current counts and sums from the source tables.

[Step 372](#step-372) · [Chapter summary](#chapter-summary)

## Step 372 — Add and verify six Manager dashboard summary cards

### Before this step
The Manager could navigate to product, sales and purchasing pages, but had to inspect each page to understand the current situation. The frozen Module 6 scope calls for low stock, out of stock, today's sales, seven-day sales, open orders and overdue orders. We use existing models and avoid storing derived figures.

### What we did and why
We introduced a query function, passed its result to the Manager home page, displayed six cards, and added tests for calculation and role isolation. The existing quick actions and other role branches remain in place.

### Code
**NEW file — `core/dashboard.py`** (complete committed file):

```python
from datetime import timedelta
from decimal import Decimal

from django.db.models import Count, F, Q, Sum
from django.utils import timezone

from catalog.models import Product
from purchasing.models import PurchaseOrder
from sales.models import Sale


def get_manager_summary():
    today = timezone.localdate()
    seven_days_ago = today - timedelta(days=6)

    active_products = Product.objects.filter(
        is_active=True,
        supplier__is_active=True
    )

    low_stock = active_products.filter(
        on_hand__gt=0,
        on_hand__lte=F('safety_stock')
    ).count()

    out_of_stock = active_products.filter(
        on_hand=0
    ).count()

    completed_sales = Sale.objects.filter(
        status='completed'
    )

    sales_today = completed_sales.filter(
        created_at__date=today
    ).aggregate(
        count=Count('id'),
        total=Sum('total')
    )

    sales_week = completed_sales.filter(
        created_at__date__gte=seven_days_ago,
        created_at__date__lte=today
    ).aggregate(
        count=Count('id'),
        total=Sum('total')
    )

    open_orders = PurchaseOrder.objects.filter(
        supplier__is_active=True,
        status__in=['ordered', 'partially_received']
    )

    overdue_orders = open_orders.filter(
        expected_delivery_date__lt=today
    )

    return {
        'low_stock_count': low_stock,
        'out_of_stock_count': out_of_stock,
        'sales_today_count': sales_today['count'],
        'sales_today_total': (
            sales_today['total'] or Decimal('0.00')
        ),
        'sales_week_count': sales_week['count'],
        'sales_week_total': (
            sales_week['total'] or Decimal('0.00')
        ),
        'open_orders_count': open_orders.count(),
        'overdue_orders_count': overdue_orders.count()
    }
```

**CLASS — `core/views.py`** (complete committed file):

```python
from django.shortcuts import render

from accounts.decorators import role_required
from .dashboard import get_manager_summary


@role_required('admin', 'manager', 'sales')
def home(request):
    context = {}

    if request.user.role == 'manager':
        context['manager_summary'] = get_manager_summary()

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (inserted Manager card section, before the Sales role branch):

```django
        <div class="row g-3 mt-3">

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Low-stock Products</h3>
                        <p class="h3">
                            {{ manager_summary.low_stock_count }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {% if manager_summary.low_stock_count == 0 %}
                                No low-stock products.
                            {% else %}
                                Active products running low.
                            {% endif %}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Out-of-stock Products</h3>
                        <p class="h3">
                            {{ manager_summary.out_of_stock_count }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {% if manager_summary.out_of_stock_count == 0 %}
                                No out-of-stock products.
                            {% else %}
                                Active products with zero stock.
                            {% endif %}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Sales Today</h3>
                        <p class="h5">
                            ₹{{ manager_summary.sales_today_total|floatformat:2 }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {{ manager_summary.sales_today_count }} completed sales
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Sales Last 7 Days</h3>
                        <p class="h5">
                            ₹{{ manager_summary.sales_week_total|floatformat:2 }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {{ manager_summary.sales_week_count }} completed sales
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Open Purchase Orders</h3>
                        <p class="h3">
                            {{ manager_summary.open_orders_count }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {% if manager_summary.open_orders_count == 0 %}
                                No open purchase orders.
                            {% else %}
                                Ordered or partially received.
                            {% endif %}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Overdue Purchase Orders</h3>
                        <p class="h3">
                            {{ manager_summary.overdue_orders_count }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {% if manager_summary.overdue_orders_count == 0 %}
                                No overdue purchase orders.
                            {% else %}
                                Open orders past their expected date.
                            {% endif %}
                        </p>
                    </div>
                </div>
            </div>

        </div>
```

**NEW file — `core/test_manager_dashboard.py`** (complete committed file):

```python
from datetime import timedelta
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from django.utils import timezone

from catalog.models import Product, Supplier
from purchasing.models import PurchaseOrder
from sales.models import Sale

from core.dashboard import get_manager_summary


class ManagerDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='dashboard_manager',
            email='dashboard_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='dashboard_sales',
            email='dashboard_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.supplier = Supplier.objects.create(
            name='Dashboard Supplier'
        )

    def create_product(self, sku, stock, safety, active=True):
        return Product.objects.create(
            sku=sku,
            name=sku,
            category='Test',
            supplier=self.supplier,
            unit_price=Decimal('100.00'),
            on_hand=stock,
            safety_stock=safety,
            is_active=active
        )

    def test_stock_counts(self):
        self.create_product('LOW', 3, 5)
        self.create_product('EMPTY', 0, 5)
        self.create_product('NORMAL', 10, 5)
        self.create_product('INACTIVE', 0, 5, False)

        summary = get_manager_summary()

        self.assertEqual(summary['low_stock_count'], 1)
        self.assertEqual(summary['out_of_stock_count'], 1)

    def test_purchase_order_counts(self):
        today = timezone.localdate()

        PurchaseOrder.objects.create(
            po_number='PO-OVERDUE',
            supplier=self.supplier,
            status='ordered',
            expected_delivery_date=today - timedelta(days=1),
            created_by=self.manager
        )

        PurchaseOrder.objects.create(
            po_number='PO-FUTURE',
            supplier=self.supplier,
            status='partially_received',
            expected_delivery_date=today + timedelta(days=2),
            created_by=self.manager
        )

        summary = get_manager_summary()

        self.assertEqual(summary['open_orders_count'], 2)
        self.assertEqual(summary['overdue_orders_count'], 1)

    def test_sales_totals_exclude_voided(self):
        Sale.objects.create(
            sale_number='SALE-001',
            created_by=self.sales_user,
            submission_key='dashboard-key-001',
            status='completed',
            total=Decimal('250.00')
        )

        Sale.objects.create(
            sale_number='SALE-002',
            created_by=self.sales_user,
            submission_key='dashboard-key-002',
            status='voided',
            total=Decimal('500.00')
        )

        summary = get_manager_summary()

        self.assertEqual(summary['sales_today_count'], 1)
        self.assertEqual(
            summary['sales_today_total'],
            Decimal('250.00')
        )
        self.assertEqual(summary['sales_week_count'], 1)

    def test_manager_sees_cards(self):
        self.client.force_login(self.manager)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Low-stock Products')
        self.assertContains(response, 'Overdue Purchase Orders')

    def test_sales_user_does_not_see_manager_cards(self):
        self.client.force_login(self.sales_user)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertNotContains(response, 'Overdue Purchase Orders')
        self.assertNotIn('manager_summary', response.context)
```

### Code walkthrough
The query function obtains today's local date and subtracts six days to form an inclusive seven-day window. It first restricts stock counts to active products belonging to active suppliers. `F('safety_stock')` compares each product's stored stock against its own threshold rather than a constant. Sales are filtered to `completed` before `Count` and `Sum` aggregate their totals, excluding voided sales. Open orders are restricted to ordered or partially received statuses, and overdue orders must also have an expected date before today. Missing money totals become `Decimal('0.00')`. The view invokes these queries only for a Manager; the template renders the resulting values using Bootstrap cards without introducing a chart dependency. Tests construct known product, order and sales records and check both the numeric answers and the absence of Manager content for Sales users.

### How we checked it
The focused command `python manage.py test core.test_manager_dashboard` passed **5 tests**. The chapter-end `python manage.py test` passed **302 tests** in 203.720 seconds. Django system checks reported zero issues, and `makemigrations --check --dry-run` reported no changes. Source commit `02bdb49` was pushed to `main` with exactly four files; the unrelated local `purchasing.zip` was not staged.

### Where to go next
Previous: [Module 5 Chapter 5](../Module-5/06-goods-receipt-and-close-short.md#step-371). Next: Module 6 Chapter 2 — low-stock and out-of-stock panel.

## Chapter summary
The Manager now sees six calculated summary cards on the existing home page. No new tables, fields or dependencies were introduced. All 302 project tests passed.

**Recall question:** Why does the sales query filter to `status='completed'` before calculating the totals?

**Source:** [Module 6 Chapter 1 commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/02bdb49).
