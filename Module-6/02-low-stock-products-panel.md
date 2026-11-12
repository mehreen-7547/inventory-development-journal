# Module 6 — Chapter 2: Low-stock and Out-of-stock Products Panel

## Before we begin
Chapter 1 introduced six summary cards. Counts alone cannot tell a Manager which products need action. This chapter adds a read-only list directly below the cards, using the same existing catalog tables.

## What we worked on
A query function selects the ten lowest-stock qualifying active products; the Manager home view passes the list to its template; the template highlights empty stock in red and low stock in amber. Six focused tests cover filtering, ordering, the ten-row limit, Manager access, Sales isolation and the empty state.

## Picture it this way
The summary card tells the Manager **how many** products need attention. The panel tells the Manager **which** products, their suppliers, and the stock thresholds and lead times needed for a purchasing decision.

[Step 373](#step-373) · [Chapter summary](#chapter-summary)

## Step 373 — Show products needing stock attention

### Before this step
The Manager saw low/out-of-stock counts but no detailed list on the dashboard. Chapter 2 must show at most ten products ordered by the smallest available stock, with supplier, SKU, name, safety stock and lead time, without showing inactive products or suppliers.

### What we did and why
Added a separate reusable query rather than mixing database logic into the HTML. The view passes its result only for the Manager role. The template uses Django conditions for Bootstrap row colors and a one-line empty state. We also corrected a missing context assignment after the first test run exposed the empty panel despite an out-of-stock product.

### Code
**CLASS — `core/dashboard.py`** (new function from committed source):

```python
def get_low_stock_products():
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
        ).select_related('supplier').order_by(
            'on_hand', 'name', 'pk'
        )[:10]
    )
```

**CLASS — `core/views.py`** (complete committed file):

```python
from django.shortcuts import render

from accounts.decorators import role_required
from .dashboard import get_manager_summary, get_low_stock_products


@role_required('admin', 'manager', 'sales')
def home(request):
    context = {}

    if request.user.role == 'manager':
        context['manager_summary'] = get_manager_summary()
        context['low_stock_products'] = get_low_stock_products()

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (added panel):

```django
<div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Low-stock and Out-of-stock Products
                </h3>
            </div>

            <div class="card-body">
                {% if low_stock_products %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>SKU</th>
                                <th>Name</th>
                                <th>Supplier</th>
                                <th>On Hand</th>
                                <th>Safety Stock</th>
                                <th>Lead Time Days</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for product in low_stock_products %}
                            <tr class="{% if product.on_hand == 0 %}table-danger{% else %}table-warning{% endif %}">
                                <td>{{ product.sku }}</td>
                                <td>{{ product.name }}</td>
                                <td>{{ product.supplier.name }}</td>
                                <td>{{ product.on_hand }}</td>
                                <td>{{ product.safety_stock }}</td>
                                <td>{{ product.lead_time_days }}</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No low-stock or out-of-stock products.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_low_stock_dashboard.py`** (complete committed file):

```python
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from catalog.models import Product, Supplier
from core.dashboard import get_low_stock_products


class LowStockDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='low_stock_manager',
            email='low_stock_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='low_stock_sales',
            email='low_stock_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.supplier = Supplier.objects.create(
            name='Active Supplier'
        )

        self.inactive_supplier = Supplier.objects.create(
            name='Inactive Supplier',
            is_active=False
        )

    def create_product(
        self, sku, stock, safety=5,
        active=True, supplier=None
    ):
        return Product.objects.create(
            sku=sku,
            name=sku,
            category='Test',
            supplier=supplier or self.supplier,
            unit_price=Decimal('100.00'),
            on_hand=stock,
            safety_stock=safety,
            lead_time_days=4,
            is_active=active
        )

    def test_only_low_and_out_of_stock_products(self):
        self.create_product('EMPTY', 0)
        self.create_product('LOW', 3)
        self.create_product('NORMAL', 10)
        self.create_product('INACTIVE', 0, active=False)
        self.create_product(
            'INACTIVE-SUPPLIER', 0,
            supplier=self.inactive_supplier
        )

        products = get_low_stock_products()
        skus = [product.sku for product in products]

        self.assertEqual(skus, ['EMPTY', 'LOW'])

    def test_lowest_stock_first(self):
        self.create_product('STOCK-4', 4)
        self.create_product('STOCK-0', 0)
        self.create_product('STOCK-2', 2)

        products = get_low_stock_products()

        self.assertEqual(
            [product.on_hand for product in products],
            [0, 2, 4]
        )

    def test_limit_to_ten_products(self):
        for number in range(15):
            self.create_product(
                f'PRODUCT-{number:02d}', 0
            )

        self.assertEqual(len(get_low_stock_products()), 10)

    def test_manager_sees_panel(self):
        self.create_product('EMPTY', 0)

        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertContains(
            response,
            'Low-stock and Out-of-stock Products'
        )
        self.assertContains(response, 'EMPTY')
        self.assertContains(response, 'table-danger')

    def test_sales_staff_cannot_see_panel(self):
        self.client.force_login(self.sales_user)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertNotContains(
            response,
            'Low-stock and Out-of-stock Products'
        )

    def test_empty_panel_message(self):
        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'No low-stock or out-of-stock products.'
        )
```

### Code walkthrough
The query begins with active products and active suppliers, so discontinued records cannot appear. Its `Q` expression includes products whose `on_hand` equals zero, or whose positive stock does not exceed their own `safety_stock`. `F('safety_stock')` performs that per-row comparison in the database. `select_related('supplier')` loads the supplier for the table without a separate query per row. Sorting by stock, then name and primary key makes the order predictable; slicing limits the panel to ten rows. The home view only adds the list to the context when the authenticated user is a Manager. In the template, zero stock uses `table-danger` and low stock uses `table-warning`. The tests also verify that Sales users cannot see the panel.

### How we checked it
The first full run discovered 308 tests and failed one Manager display test because `low_stock_products` had not been added to the view context. The view was corrected. The subsequent full test run passed **308 tests**; Django checks reported zero issues and no pending migrations. Git initially rejected trailing whitespace on the template's line 172; the whitespace was removed, `git diff --cached --check` passed, and source commit `81cde18` was pushed to `main` with four files. The LF/CRLF warning was informational.

### Where to go next
Previous: [Step 372](01-manager-dashboard-summary-cards.md#step-372). Next: Module 6 Chapter 3 — purchase orders needing attention.

## Chapter summary
The Manager dashboard now shows up to ten out-of-stock or low-stock products, sorted by quantity, with supplier details and red/amber row indicators. No new models, database fields or libraries were added. All 308 tests passed before the whitespace-only correction.

**Recall question:** Why do we use `F('safety_stock')` rather than a fixed number when finding low-stock products?

**Source:** [Chapter 2 source commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/81cde18).
