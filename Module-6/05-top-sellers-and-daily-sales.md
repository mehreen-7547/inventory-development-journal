# Module 6 — Chapter 5: Top-selling Products and Seven-day Sales Activity

## Before we begin
After summary counts, stock alerts, purchase-order attention and recent activity, Managers need a view of the products driving unit volume and a short view of daily revenue. Chapter 5 adds two read-only panels using the existing sales and sales-line models.

## What we worked on
We added two database-backed functions in `core/dashboard.py`: a 30-day top-five product ranking and a seven-calendar-day completed-sales summary. The existing Manager home view supplies the results and empty-state flag. The template adds a ranked table and Bootstrap progress bars. Eleven new tests verify calculations, date boundaries, discounts, void exclusions, panel access and empty states.

## Step 376 — Rank products and display seven-day sales

### Before this step
The Manager could see recent transactions but no comparison of top-selling products or day-by-day sales activity. The approved scope requires the highest five products by units over 30 days and seven consecutive days, including zero-sale days, without a charting dependency.

### What we did and why
`SaleLine` supplies per-product quantities and gross line revenue, and `Sale` supplies discounted transaction totals. Both exclude voided sales from statistics. A database grouping collects daily sales totals; a seven-day Python loop fills missing dates with zeros and calculates a relative bar width against the highest-revenue day. The result remains computed at request time rather than stored.

### Code
**CLASS — `core/dashboard.py`** (new imports and functions; existing functions retained):

```python
from django.db.models.functions import TruncDate
from sales.models import Sale, SaleLine

def get_top_selling_products():
    today = timezone.localdate()
    start_date = today - timedelta(days=29)

    return list(
        SaleLine.objects.filter(
            sale__status='completed',
            sale__created_at__date__gte=start_date,
            sale__created_at__date__lte=today,
            product__is_active=True,
            product__supplier__is_active=True
        ).values(
            'product_id',
            product_label=F('product__name')
        ).annotate(
            units_sold=Sum('quantity'),
            gross_revenue=Sum('line_total')
        ).order_by(
            '-units_sold',
            'product_label',
            'product_id'
        )[:5]
    )


def get_daily_sales_last_seven_days():
    today = timezone.localdate()
    start_date = today - timedelta(days=6)

    sales_by_day = (
        Sale.objects.filter(
            status='completed',
            created_at__date__gte=start_date,
            created_at__date__lte=today
        ).annotate(
            sale_date=TruncDate('created_at')
        ).values(
            'sale_date'
        ).annotate(
            sales_count=Count('id'),
            total=Sum('total')
        ).order_by('sale_date')
    )

    totals = {
        item['sale_date']: item
        for item in sales_by_day
    }

    days = []

    for number in range(7):
        day = start_date + timedelta(days=number)
        item = totals.get(day)

        days.append({
            'date': day,
            'sales_count': (
                item['sales_count'] if item else 0
            ),
            'total': (
                item['total'] if item
                else Decimal('0.00')
            )
        })

    highest_total = max(
        item['total'] for item in days
    )

    for item in days:
        if highest_total > 0:
            item['bar_percent'] = int(
                item['total'] * 100 / highest_total
            )
        else:
            item['bar_percent'] = 0

    return days
```

**CLASS — `core/views.py`** (full committed file):

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
    get_daily_sales_last_seven_days
)


@role_required('admin', 'manager', 'sales')
def home(request):
    context = {}

    if request.user.role == 'manager':
        context['manager_summary'] = get_manager_summary()
        context['low_stock_products'] = get_low_stock_products()

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

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (new top products and daily sales panels):

```django
<div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Top-selling Products (Last 30 Days)
                </h3>
            </div>

            <div class="card-body">
                {% if top_selling_products %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Product</th>
                                <th>Units Sold</th>
                                <th>Gross Revenue (Before Discounts)</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for item in top_selling_products %}
                            <tr>
                                <td>{{ item.product_label }}</td>
                                <td>{{ item.units_sold }}</td>
                                <td>
                                    ₹{{ item.gross_revenue|floatformat:2 }}
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No completed product sales in the last 30 days.
                </p>
                {% endif %}
            </div>
        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Daily Sales (Last 7 Days)
                </h3>
            </div>

            <div class="card-body">
                {% if daily_sales_has_data %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Date</th>
                                <th>Completed Sales</th>
                                <th>Total Revenue</th>
                                <th>Sales Activity</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for item in daily_sales %}
                            <tr>
                                <td>
                                    {{ item.date|date:"d M Y" }}
                                </td>
                                <td>{{ item.sales_count }}</td>
                                <td>
                                    ₹{{ item.total|floatformat:2 }}
                                </td>
                                <td style="min-width: 170px;">
                                    <div
                                        class="progress"
                                        role="progressbar"
                                        aria-label="Daily sales relative to highest day"
                                        aria-valuenow="{{ item.bar_percent }}"
                                        aria-valuemin="0"
                                        aria-valuemax="100"
                                    >
                                        <div
                                            class="progress-bar"
                                            style="width: {{ item.bar_percent }}%;">
                                        </div>
                                    </div>
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>

                <p class="small text-secondary mb-0">
                    Bars compare daily revenue with the
                    highest-revenue day in this 7-day period.
                </p>
                {% else %}
                <p class="text-secondary mb-0">
                    No completed sales in the last 7 days.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_sales_trends_dashboard.py`** (full committed file):

```python
from datetime import datetime, time, timedelta
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from django.utils import timezone

from catalog.models import Product, Supplier
from sales.models import Sale, SaleLine

from core.dashboard import (
    get_top_selling_products,
    get_daily_sales_last_seven_days
)


class SalesTrendsDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='trends_manager',
            email='trends_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='trends_sales',
            email='trends_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.admin = User.objects.create_user(
            username='trends_admin',
            email='trends_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        self.supplier = Supplier.objects.create(
            name='Trends Supplier'
        )

        self.inactive_supplier = Supplier.objects.create(
            name='Inactive Trends Supplier',
            is_active=False
        )

        self.product = self.create_product('MAIN-PRODUCT')
        self.today = timezone.localdate()

    def create_product(
        self, sku, active=True, supplier=None
    ):
        return Product.objects.create(
            sku=sku,
            name=sku,
            category='Test',
            supplier=supplier or self.supplier,
            unit_price=Decimal('10.00'),
            is_active=active
        )

    def create_sale(
        self, number, days_ago=0,
        status='completed', lines=None, total=None
    ):
        if lines is None:
            lines = [(self.product, 1)]

        subtotal = sum(
            (
                product.unit_price * quantity
                for product, quantity in lines
            ),
            Decimal('0.00')
        )

        if total is None:
            sale_total = subtotal
        else:
            sale_total = Decimal(str(total))

        discount = subtotal - sale_total

        sale = Sale.objects.create(
            sale_number=number,
            submission_key=number,
            created_by=self.sales_user,
            status=status,
            subtotal=subtotal,
            total=sale_total,
            discount_type=(
                'fixed' if discount > 0 else 'none'
            ),
            discount_value=(
                discount if discount > 0
                else Decimal('0.00')
            )
        )

        for product, quantity in lines:
            SaleLine.objects.create(
                sale=sale,
                product=product,
                product_name=product.name,
                quantity=quantity,
                unit_price=product.unit_price,
                line_total=product.unit_price * quantity
            )

        sale_date = self.today - timedelta(days=days_ago)

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

    def test_top_products_rank_by_units_and_sum_revenue(self):
        second = self.create_product('SECOND-PRODUCT')

        self.create_sale(
            'TOP-001',
            lines=[
                (self.product, 2),
                (second, 5)
            ]
        )

        self.create_sale(
            'TOP-002',
            lines=[(self.product, 4)]
        )

        results = get_top_selling_products()

        self.assertEqual(
            [item['product_label'] for item in results],
            ['MAIN-PRODUCT', 'SECOND-PRODUCT']
        )

        self.assertEqual(results[0]['units_sold'], 6)
        self.assertEqual(
            results[0]['gross_revenue'],
            Decimal('60.00')
        )

    def test_top_products_show_only_five(self):
        for number in range(1, 8):
            product = self.create_product(
                f'RANK-{number}'
            )

            self.create_sale(
                f'RANK-SALE-{number}',
                lines=[(product, number)]
            )

        results = get_top_selling_products()

        self.assertEqual(len(results), 5)
        self.assertEqual(
            results[0]['product_label'],
            'RANK-7'
        )
        self.assertEqual(
            results[-1]['product_label'],
            'RANK-3'
        )

    def test_top_products_exclude_voided_and_older_sales(self):
        self.create_sale(
            'WITHIN-30',
            days_ago=29,
            lines=[(self.product, 2)]
        )

        self.create_sale(
            'OUTSIDE-30',
            days_ago=30,
            lines=[(self.product, 20)]
        )

        self.create_sale(
            'VOIDED-30',
            status='voided',
            lines=[(self.product, 30)]
        )

        results = get_top_selling_products()

        self.assertEqual(len(results), 1)
        self.assertEqual(results[0]['units_sold'], 2)

    def test_top_products_require_active_product_and_supplier(self):
        inactive_product = self.create_product(
            'INACTIVE-PRODUCT',
            active=False
        )

        inactive_supplier_product = self.create_product(
            'INACTIVE-SUPPLIER-PRODUCT',
            supplier=self.inactive_supplier
        )

        self.create_sale(
            'ACTIVE-SALE',
            lines=[(self.product, 2)]
        )

        self.create_sale(
            'INACTIVE-PRODUCT-SALE',
            lines=[(inactive_product, 10)]
        )

        self.create_sale(
            'INACTIVE-SUPPLIER-SALE',
            lines=[(inactive_supplier_product, 20)]
        )

        results = get_top_selling_products()

        self.assertEqual(len(results), 1)
        self.assertEqual(
            results[0]['product_label'],
            'MAIN-PRODUCT'
        )

    def test_top_product_revenue_is_before_discounts(self):
        self.create_sale(
            'DISCOUNTED-SALE',
            lines=[(self.product, 4)],
            total=Decimal('30.00')
        )

        products = get_top_selling_products()
        days = get_daily_sales_last_seven_days()

        self.assertEqual(
            products[0]['gross_revenue'],
            Decimal('40.00')
        )

        self.assertEqual(
            days[-1]['total'],
            Decimal('30.00')
        )

    def test_daily_sales_covers_all_seven_dates(self):
        days = get_daily_sales_last_seven_days()

        self.assertEqual(len(days), 7)
        self.assertEqual(
            days[0]['date'],
            self.today - timedelta(days=6)
        )
        self.assertEqual(days[-1]['date'], self.today)

        for item in days:
            self.assertEqual(item['sales_count'], 0)
            self.assertEqual(
                item['total'],
                Decimal('0.00')
            )
            self.assertEqual(item['bar_percent'], 0)

    def test_daily_sales_only_completed_and_aggregates_totals(self):
        self.create_sale(
            'TODAY-001',
            lines=[(self.product, 3)]
        )

        self.create_sale(
            'TODAY-002',
            lines=[(self.product, 2)]
        )

        self.create_sale(
            'TODAY-VOIDED',
            status='voided',
            lines=[(self.product, 9)]
        )

        self.create_sale(
            'YESTERDAY-001',
            days_ago=1
        )

        days = get_daily_sales_last_seven_days()

        self.assertEqual(days[-1]['sales_count'], 2)
        self.assertEqual(
            days[-1]['total'],
            Decimal('50.00')
        )

        self.assertEqual(days[-2]['sales_count'], 1)
        self.assertEqual(
            days[-2]['total'],
            Decimal('10.00')
        )

    def test_daily_sales_includes_first_day_and_excludes_previous(self):
        self.create_sale(
            'FIRST-DAY',
            days_ago=6,
            lines=[(self.product, 2)]
        )

        self.create_sale(
            'BEFORE-WINDOW',
            days_ago=7,
            lines=[(self.product, 10)]
        )

        days = get_daily_sales_last_seven_days()

        self.assertEqual(days[0]['sales_count'], 1)
        self.assertEqual(
            days[0]['total'],
            Decimal('20.00')
        )

        self.assertEqual(
            sum(item['sales_count'] for item in days),
            1
        )

    def test_daily_sales_bar_percentages(self):
        self.create_sale(
            'HIGHEST-DAY',
            lines=[(self.product, 10)]
        )

        self.create_sale(
            'HALF-DAY',
            days_ago=1,
            lines=[(self.product, 5)]
        )

        days = get_daily_sales_last_seven_days()

        self.assertEqual(days[-1]['bar_percent'], 100)
        self.assertEqual(days[-2]['bar_percent'], 50)
        self.assertEqual(days[0]['bar_percent'], 0)

    def test_manager_sees_panels_only(self):
        self.create_sale(
            'VISIBLE-SALE',
            lines=[(self.product, 3)]
        )

        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertContains(
            response,
            'Top-selling Products (Last 30 Days)'
        )
        self.assertContains(
            response,
            'Daily Sales (Last 7 Days)'
        )
        self.assertContains(response, 'MAIN-PRODUCT')
        self.assertContains(
            response,
            'Gross Revenue (Before Discounts)'
        )

        for user in [self.sales_user, self.admin]:
            with self.subTest(role=user.role):
                self.client.force_login(user)
                response = self.client.get(reverse('home'))

                self.assertEqual(response.status_code, 200)
                self.assertNotContains(
                    response,
                    'Top-selling Products (Last 30 Days)'
                )
                self.assertNotContains(
                    response,
                    'Daily Sales (Last 7 Days)'
                )
                self.assertNotIn(
                    'top_selling_products',
                    response.context
                )

    def test_manager_empty_messages(self):
        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'No completed product sales in the last 30 days.'
        )

        self.assertContains(
            response,
            'No completed sales in the last 7 days.'
        )
```

### Code walkthrough
`get_top_selling_products()` filters to the past 30 local dates, completed sales and currently active products/suppliers. It groups by product, totals `quantity` and `line_total`, sorts by units sold descending and keeps five rows. Because discounts are applied at the sale level, the per-product value is explicitly labelled **Gross Revenue (Before Discounts)**, not net revenue.

`get_daily_sales_last_seven_days()` uses `TruncDate` to group completed `Sale` rows by local date, aggregating number and net `total`. It fills each of seven dates from six days ago through today. It computes bar percentages relative to the maximum daily total; an all-zero week yields zero widths. The view passes an additional flag so an all-zero week displays a one-line empty state. The dashboard panels remain Manager-only.

### How we checked it
The full Django suite passed **336 tests** in **616.410 seconds**, with zero system-check issues and no pending migrations. Git's staged whitespace check passed and four files were committed as `1e64eb7` on source `main`. LF/CRLF conversion warnings were informational. The CSS validator warnings on `style="width: {{ item.bar_percent }}%;"` were editor false positives: Django renders the percentage to a valid CSS value.

### Where to go next
Previous: [Step 375](04-recent-stock-movements-and-sales.md#step-375). Next: Module 6 Chapter 6 — Sales Staff dashboard.

## Chapter summary
The Manager dashboard now presents a top-five 30-day units ranking with gross line revenue and a seven-day completed-sales table with relative progress bars. Eleven new tests were added; the complete suite passed 336 tests.

**Recall question:** Why should the top-seller revenue column say **before discounts** when the daily revenue column does not?

**Source:** [Chapter 5 source commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/1e64eb7).
