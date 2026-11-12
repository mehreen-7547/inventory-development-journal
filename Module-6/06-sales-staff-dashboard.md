# Module 6 — Chapter 6: Sales Staff Dashboard

## Before this chapter
The Manager dashboard was completed in Chapters 1–5, while Sales Staff had three navigation actions but no personal performance figures. This chapter adds the approved read-only Sales dashboard, preserving the Manager and Admin views.

## What we did
We added functions to calculate the signed-in staff member's sales counts and totals, select their eight most recent sales, and identify eight active products running low or out of stock. We connected the functions to the existing role-based home view and presented the data in Bootstrap cards and tables. No new models, database fields, apps or libraries were needed.

## Step 377 — Sales Staff Dashboard

### Before this step
Sales users could record sales and view their own history, but could not see today's and this week's totals or a compact stock alert view directly from the dashboard.

### Code

**CLASS — `core/dashboard.py`** (three added functions, verbatim from source `a800ee7`; keep all previous functions):

```python
def get_sales_staff_summary(user):
    today = timezone.localdate()
    seven_days_ago = today - timedelta(days=6)

    completed_sales = Sale.objects.filter(
        created_by=user,
        status='completed'
    )

    today_sales = completed_sales.filter(
        created_at__date=today
    ).aggregate(
        count=Count('id'),
        total=Sum('total')
    )

    week_sales = completed_sales.filter(
        created_at__date__gte=seven_days_ago,
        created_at__date__lte=today
    ).aggregate(
        count=Count('id'),
        total=Sum('total')
    )

    out_of_stock_count = Product.objects.filter(
        is_active=True,
        supplier__is_active=True,
        on_hand=0
    ).count()

    return {
        'today_count': today_sales['count'],
        'today_total': (
            today_sales['total'] or Decimal('0.00')
        ),
        'week_count': week_sales['count'],
        'week_total': (
            week_sales['total'] or Decimal('0.00')
        ),
        'out_of_stock_count': out_of_stock_count
    }


def get_sales_staff_recent_sales(user):
    return list(
        Sale.objects.filter(
            created_by=user
        ).order_by(
            '-created_at',
            '-pk'
        )[:8]
    )


def get_sales_staff_stock_alerts():
    return list(
        Product.objects.filter(
            is_active=True,
            supplier__is_active=True
        ).filter(
            Q(on_hand=0)
            | Q(
                on_hand__gt=0,
                on_hand__lte=F('safety_stock')
            )
        ).order_by(
            'on_hand',
            'name',
            'pk'
        )[:8]
    )
```

**CLASS — `core/views.py`** (complete committed file):

```python
from django.shortcuts import render

from accounts.decorators import role_required
from .dashboard import (
    get_manager_summary,
    get_low_stock_products,
    get_purchase_orders_needing_attention,
    get_recent_stock_movements,
    get_recent_sales,
    get_top_selling_products,
    get_daily_sales_last_seven_days,
    get_sales_staff_summary,
    get_sales_staff_recent_sales,
    get_sales_staff_stock_alerts
)


@role_required('admin', 'manager', 'sales')
def home(request):
    context = {}

    if request.user.role == 'manager':
        context['manager_summary'] = get_manager_summary()

        context['low_stock_products'] = (
            get_low_stock_products()
        )

        context['purchase_orders_attention'] = (
            get_purchase_orders_needing_attention()
        )

        context['recent_stock_movements'] = (
            get_recent_stock_movements()
        )

        context['recent_sales'] = get_recent_sales()

        context['top_selling_products'] = (
            get_top_selling_products()
        )

        context['daily_sales'] = (
            get_daily_sales_last_seven_days()
        )

        context['daily_sales_has_data'] = any(
            item['sales_count'] > 0
            for item in context['daily_sales']
        )

    elif request.user.role == 'sales':
        context['sales_staff_summary'] = (
            get_sales_staff_summary(request.user)
        )

        context['sales_staff_recent_sales'] = (
            get_sales_staff_recent_sales(request.user)
        )

        context['sales_staff_stock_alerts'] = (
            get_sales_staff_stock_alerts()
        )

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (added Sales Staff section after the existing three action links):

```django
<div class="row g-3 mt-3">

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">My Sales Today</h3>
                        <p class="h5">
                            ₹{{ sales_staff_summary.today_total|floatformat:2 }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {{ sales_staff_summary.today_count }} completed sales
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">My Sales Last 7 Days</h3>
                        <p class="h5">
                            ₹{{ sales_staff_summary.week_total|floatformat:2 }}
                        </p>
                        <p class="small text-secondary mb-0">
                            {{ sales_staff_summary.week_count }} completed sales
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-4">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Out-of-stock Products</h3>
                        <p class="h3">
                            {{ sales_staff_summary.out_of_stock_count }}
                        </p>
                        <p class="small text-secondary mb-0">
                            Active products currently unavailable.
                        </p>
                    </div>
                </div>
            </div>

        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">My Recent Sales</h3>
            </div>

            <div class="card-body">
                {% if sales_staff_recent_sales %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Sale Number</th>
                                <th>Date and Time</th>
                                <th>Total</th>
                                <th>Status</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for sale in sales_staff_recent_sales %}
                            <tr>
                                <td>
                                    <a href="{% url 'sale_detail' sale.pk %}">
                                        {{ sale.sale_number }}
                                    </a>
                                </td>
                                <td>
                                    {{ sale.created_at|date:"d M Y, h:i A" }}
                                </td>
                                <td>
                                    ₹{{ sale.total|floatformat:2 }}
                                </td>
                                <td>
                                    {% if sale.status == 'voided' %}
                                    <span class="badge bg-danger">
                                        Voided
                                    </span>
                                    {% else %}
                                    <span class="badge bg-success">
                                        Completed
                                    </span>
                                    {% endif %}
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    You have not recorded any sales yet.
                </p>
                {% endif %}
            </div>
        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Products Needing Stock Attention
                </h3>
            </div>

            <div class="card-body">
                {% if sales_staff_stock_alerts %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Product</th>
                                <th>On Hand</th>
                                <th>Stock Status</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for product in sales_staff_stock_alerts %}
                            <tr class="{% if product.on_hand == 0 %}table-danger{% else %}table-warning{% endif %}">
                                <td>{{ product.name }}</td>
                                <td>{{ product.on_hand }}</td>
                                <td>
                                    {% if product.on_hand == 0 %}
                                    <span class="badge bg-danger">
                                        Out of Stock
                                    </span>
                                    {% else %}
                                    <span class="badge bg-warning text-dark">
                                        Low Stock
                                    </span>
                                    {% endif %}
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No products currently need stock attention.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_sales_staff_dashboard.py`** (complete committed tests):

```python
from datetime import datetime, time, timedelta
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from django.utils import timezone

from catalog.models import Product, Supplier
from sales.models import Sale

from core.dashboard import (
    get_sales_staff_summary,
    get_sales_staff_recent_sales,
    get_sales_staff_stock_alerts
)


class SalesStaffDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.sales_user = User.objects.create_user(
            username='staff_dashboard_sales',
            email='staff_dashboard_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.other_sales = User.objects.create_user(
            username='other_dashboard_sales',
            email='other_dashboard_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.manager = User.objects.create_user(
            username='staff_dashboard_manager',
            email='staff_dashboard_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.admin = User.objects.create_user(
            username='staff_dashboard_admin',
            email='staff_dashboard_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        self.supplier = Supplier.objects.create(
            name='Active Supplier'
        )

        self.inactive_supplier = Supplier.objects.create(
            name='Inactive Supplier',
            is_active=False
        )

        self.today = timezone.localdate()

    def create_product(
        self, sku, stock=0, safety=5,
        active=True, supplier=None
    ):
        return Product.objects.create(
            sku=sku,
            name=sku,
            category='Test',
            supplier=supplier or self.supplier,
            unit_price=Decimal('50.00'),
            on_hand=stock,
            safety_stock=safety,
            is_active=active
        )

    def create_sale(
        self, number, user=None, days_ago=0,
        status='completed', total='100.00'
    ):
        amount = Decimal(total)

        sale = Sale.objects.create(
            sale_number=number,
            submission_key=number,
            created_by=user or self.sales_user,
            status=status,
            subtotal=amount,
            total=amount
        )

        sale_date = self.today - timedelta(
            days=days_ago
        )

        sale_time = timezone.make_aware(
            datetime.combine(
                sale_date,
                time(12, 0)
            )
        )

        Sale.objects.filter(pk=sale.pk).update(
            created_at=sale_time
        )

        return sale

    def test_summary_counts_only_own_completed_sales(self):
        self.create_sale(
            'OWN-TODAY',
            total='100.00'
        )

        self.create_sale(
            'OWN-WEEK',
            days_ago=3,
            total='40.00'
        )

        self.create_sale(
            'OTHER-TODAY',
            user=self.other_sales,
            total='500.00'
        )

        self.create_sale(
            'OWN-VOIDED',
            status='voided',
            total='200.00'
        )

        summary = get_sales_staff_summary(
            self.sales_user
        )

        self.assertEqual(summary['today_count'], 1)
        self.assertEqual(
            summary['today_total'],
            Decimal('100.00')
        )

        self.assertEqual(summary['week_count'], 2)
        self.assertEqual(
            summary['week_total'],
            Decimal('140.00')
        )

    def test_seven_day_boundaries(self):
        self.create_sale(
            'WITHIN-WEEK',
            days_ago=6,
            total='30.00'
        )

        self.create_sale(
            'OUTSIDE-WEEK',
            days_ago=7,
            total='90.00'
        )

        summary = get_sales_staff_summary(
            self.sales_user
        )

        self.assertEqual(summary['today_count'], 0)
        self.assertEqual(summary['week_count'], 1)
        self.assertEqual(
            summary['week_total'],
            Decimal('30.00')
        )

    def test_empty_sales_totals_are_zero(self):
        summary = get_sales_staff_summary(
            self.sales_user
        )

        self.assertEqual(summary['today_count'], 0)
        self.assertEqual(summary['week_count'], 0)

        self.assertEqual(
            summary['today_total'],
            Decimal('0.00')
        )

        self.assertEqual(
            summary['week_total'],
            Decimal('0.00')
        )

    def test_out_of_stock_count_uses_active_products(self):
        self.create_product('OUT', stock=0)
        self.create_product('LOW', stock=2)
        self.create_product('NORMAL', stock=10)

        self.create_product(
            'INACTIVE-PRODUCT',
            stock=0,
            active=False
        )

        self.create_product(
            'INACTIVE-SUPPLIER',
            stock=0,
            supplier=self.inactive_supplier
        )

        summary = get_sales_staff_summary(
            self.sales_user
        )

        self.assertEqual(
            summary['out_of_stock_count'],
            1
        )

    def test_stock_alerts_filter_and_order(self):
        self.create_product('LOW', stock=3)
        self.create_product('OUT', stock=0)
        self.create_product('NORMAL', stock=10)

        self.create_product(
            'INACTIVE',
            stock=0,
            active=False
        )

        self.create_product(
            'INACTIVE-SUPPLIER',
            stock=0,
            supplier=self.inactive_supplier
        )

        products = get_sales_staff_stock_alerts()

        self.assertEqual(
            [product.sku for product in products],
            ['OUT', 'LOW']
        )

    def test_stock_alerts_limited_to_eight(self):
        for number in range(12):
            self.create_product(
                f'LIMIT-{number:02d}',
                stock=0
            )

        products = get_sales_staff_stock_alerts()

        self.assertEqual(len(products), 8)
        self.assertEqual(
            products[0].sku,
            'LIMIT-00'
        )

    def test_recent_sales_are_personal_and_include_voided(self):
        self.create_sale('OWN-001')

        self.create_sale(
            'OWN-002',
            status='voided'
        )

        self.create_sale(
            'OTHER-001',
            user=self.other_sales
        )

        sales = get_sales_staff_recent_sales(
            self.sales_user
        )

        self.assertEqual(
            [sale.sale_number for sale in sales],
            ['OWN-002', 'OWN-001']
        )

        self.assertEqual(
            sales[0].status,
            'voided'
        )

    def test_recent_sales_limited_to_eight(self):
        for number in range(12):
            self.create_sale(
                f'OWN-{number:03d}'
            )

        sales = get_sales_staff_recent_sales(
            self.sales_user
        )

        self.assertEqual(len(sales), 8)
        self.assertEqual(
            sales[0].sale_number,
            'OWN-011'
        )

    def test_sales_dashboard_displays_own_information(self):
        sale = self.create_sale('VISIBLE-SALE')

        self.create_sale(
            'HIDDEN-COWORKER',
            user=self.other_sales
        )

        self.create_product(
            'EMPTY-PRODUCT',
            stock=0
        )

        self.client.force_login(self.sales_user)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertContains(
            response,
            'My Sales Today'
        )

        self.assertContains(
            response,
            'My Sales Last 7 Days'
        )

        self.assertContains(
            response,
            'My Recent Sales'
        )

        self.assertContains(
            response,
            'Products Needing Stock Attention'
        )

        self.assertContains(
            response,
            'VISIBLE-SALE'
        )

        self.assertContains(
            response,
            'EMPTY-PRODUCT'
        )

        self.assertContains(
            response,
            reverse('sale_detail', args=[sale.pk])
        )

        self.assertNotContains(
            response,
            'HIDDEN-COWORKER'
        )

        self.assertNotContains(
            response,
            'Purchase Orders Needing Attention'
        )

        self.assertNotContains(
            response,
            'Recent Stock Movements'
        )

        self.assertNotContains(
            response,
            'Active Supplier'
        )

    def test_sales_dashboard_empty_messages(self):
        self.client.force_login(self.sales_user)

        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'You have not recorded any sales yet.'
        )

        self.assertContains(
            response,
            'No products currently need stock attention.'
        )

    def test_manager_does_not_receive_sales_staff_panels(self):
        self.client.force_login(self.manager)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertNotContains(
            response,
            'My Recent Sales'
        )

        self.assertNotContains(
            response,
            'My Sales Today'
        )

        self.assertNotIn(
            'sales_staff_summary',
            response.context
        )

    def test_admin_does_not_receive_sales_staff_panels(self):
        self.client.force_login(self.admin)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertNotContains(
            response,
            'My Recent Sales'
        )

        self.assertNotContains(
            response,
            'My Sales Last 7 Days'
        )

        self.assertNotIn(
            'sales_staff_summary',
            response.context
        )
```

**CLASS — `core/test_recent_activity_dashboard.py`** (corrected earlier regression test, now indented inside the class):

```python
def test_sales_staff_cannot_see_manager_activity(self):
        self.create_movement()
        self.create_sale('SALE-PRIVATE')

        Sale.objects.create(
            sale_number='MANAGER-ONLY-SALE',
            submission_key='manager-only-activity',
            created_by=self.manager,
            subtotal=Decimal('50.00'),
            total=Decimal('50.00')
        )

        self.client.force_login(self.sales_user)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertNotContains(
            response,
            'Recent Stock Movements'
        )

        self.assertNotIn(
            'recent_stock_movements',
            response.context
        )

        self.assertNotIn(
            'recent_sales',
            response.context
        )

        self.assertContains(
            response,
            'My Recent Sales'
        )

        self.assertContains(
            response,
            'SALE-PRIVATE'
        )

        self.assertNotContains(
            response,
            'MANAGER-ONLY-SALE'
        )
```

### Code walkthrough
- `get_sales_staff_summary(user)` filters `Sale` by the authenticated creator **and** completed status, then calculates counts and discounted totals for today and the last seven days (including today). It also counts currently active products with zero stock, excluding inactive suppliers.
- `get_sales_staff_recent_sales(user)` reads only that employee's latest eight sales. Voided sales remain visible in history, even though they do not count towards performance totals.
- `get_sales_staff_stock_alerts()` selects active zero-stock and low-stock products, orders by on-hand stock, and limits results to eight. Sales staff see product names and quantities but not supplier identities or purchasing information.
- `home(request)` keeps Manager-only data in the Manager branch. A separate `elif role == 'sales'` supplies just the three Sales Staff data structures. The template preserves the three existing quick actions.
- The revised Chapter 4 regression test checks both access boundaries: a staff member sees **My Recent Sales**, including their own sale, but cannot see another person's sale or Manager-only context variables.

### How we checked it
The twelve new tests passed. The first regression pass identified an old assertion incompatible with the newly approved personal Recent Sales panel; its test was revised. After source commit `9bc0d92`, only 48 tests were discovered because the revised regression test had been accidentally dedented and two following tests were no longer discovered. Commit `a800ee7` restored indentation and test discovery. The user confirmed **51 dashboard tests passed in 58.205 seconds** and that `a800ee7` was pushed to `main`. The most recent complete-project run remains **336 tests passed** at Chapter 5; the expected total now is **348 tests**, pending a full rerun.

### Next
Previous: [Step 376](05-top-sellers-and-daily-sales.md#step-376). Next: Module 6 Chapter 7 — Administrator Dashboard.

## Chapter summary
Sales Staff now have personal completed-sales totals for today and the last seven days, eight of their own recent sales, an overall out-of-stock count and eight low/out-of-stock product alerts. Business-wide sales, purchasing details and stock movements remain Manager-only.

**Recall question:** Why are voided sales shown in `My Recent Sales` but excluded from `My Sales Today`?

**Source:** [Chapter 6 feature commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/9bc0d92) · [test discovery correction](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/a800ee7).
