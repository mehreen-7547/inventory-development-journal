# Module 6 — Chapter 4: Recent Stock Movements and Sales

## Before we begin
The Manager dashboard already shows summary cards, low-stock products and open purchase orders. Chapter 4 adds two read-only activity panels so the Manager can see what changed recently and which sales were recorded or voided.

## What we worked on
Two query functions in `core/dashboard.py` retrieve eight recent stock movements and five recent sales. The existing Manager home view passes the results to the template, which displays a table for each and a short message when no records exist. Ten focused tests verify ordering, limits, active-product filtering, voided sales, role isolation and empty states.

## Step 375 — Show recent activity on the Manager dashboard

### Before this step
The Manager could see inventory shortages and pending supplier orders but not the most recent stock changes or sales on the dashboard.

### What we did and why
We reused the existing `StockMovement` and `Sale` models without adding any stored totals or new tables. Stock movements are filtered to active products and active suppliers, while the recent sales history intentionally includes voided sales. Both queries order by newest creation time and use primary key to break ties.

### Code
**CLASS — `core/dashboard.py`** (new import and functions; preserve preceding functions):

```python
from inventory.models import StockMovement

def get_recent_stock_movements():
    return list(
        StockMovement.objects.filter(
            product__is_active=True,
            product__supplier__is_active=True
        ).select_related(
            'product',
            'created_by'
        ).order_by(
            '-created_at',
            '-pk'
        )[:8]
    )


def get_recent_sales():
    return list(
        Sale.objects.select_related(
            'created_by'
        ).order_by(
            '-created_at',
            '-pk'
        )[:5]
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
    get_recent_sales
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

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (added activity panels):

```django
<div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">Recent Stock Movements</h3>
            </div>

            <div class="card-body">
                {% if recent_stock_movements %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Time</th>
                                <th>Product</th>
                                <th>Movement Type</th>
                                <th>Quantity Change</th>
                                <th>Done By</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for movement in recent_stock_movements %}
                            <tr>
                                <td>
                                    {{ movement.created_at|date:"d M Y, h:i A" }}
                                </td>
                                <td>{{ movement.product.name }}</td>
                                <td>
                                    {{ movement.get_movement_type_display }}
                                </td>
                                <td>
                                    {% if movement.quantity_delta > 0 %}
                                    <span class="text-success fw-semibold">
                                        +{{ movement.quantity_delta }}
                                    </span>
                                    {% else %}
                                    <span class="text-danger fw-semibold">
                                        {{ movement.quantity_delta }}
                                    </span>
                                    {% endif %}
                                </td>
                                <td>{{ movement.created_by.username }}</td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No stock movements recorded yet.
                </p>
                {% endif %}
            </div>
        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">Recent Sales</h3>
            </div>

            <div class="card-body">
                {% if recent_sales %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Sale Number</th>
                                <th>Time</th>
                                <th>Total</th>
                                <th>Staff</th>
                                <th>Status</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for sale in recent_sales %}
                            <tr>
                                <td>{{ sale.sale_number }}</td>
                                <td>
                                    {{ sale.created_at|date:"d M Y, h:i A" }}
                                </td>
                                <td>
                                    ₹{{ sale.total|floatformat:2 }}
                                </td>
                                <td>{{ sale.created_by.username }}</td>
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
                    No sales recorded yet.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_recent_activity_dashboard.py`** (complete committed file):

```python
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from catalog.models import Product, Supplier
from inventory.models import StockMovement
from inventory.services import change_stock
from sales.models import Sale

from core.dashboard import (
    get_recent_stock_movements,
    get_recent_sales
)


class RecentActivityDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='activity_manager',
            email='activity_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='activity_sales',
            email='activity_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.admin = User.objects.create_user(
            username='activity_admin',
            email='activity_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        self.supplier = Supplier.objects.create(
            name='Activity Supplier'
        )

        self.inactive_supplier = Supplier.objects.create(
            name='Inactive Activity Supplier',
            is_active=False
        )

        self.product = Product.objects.create(
            sku='ACT-001',
            name='Activity Product',
            category='Test',
            supplier=self.supplier,
            unit_price=Decimal('100.00'),
            safety_stock=5,
            lead_time_days=3
        )

    def create_movement(self, product=None, quantity=1):
        return change_stock(
            product_id=(product or self.product).pk,
            delta=quantity,
            movement_type='opening',
            reason='Dashboard test',
            user=self.manager
        )

    def create_sale(self, number, status='completed'):
        return Sale.objects.create(
            sale_number=number,
            submission_key=number,
            created_by=self.sales_user,
            status=status,
            subtotal=Decimal('100.00'),
            total=Decimal('100.00')
        )

    def test_recent_movements_newest_first(self):
        self.create_movement(quantity=2)
        self.create_movement(quantity=3)
        self.create_movement(quantity=4)

        movements = get_recent_stock_movements()

        self.assertEqual(
            [movement.quantity_delta for movement in movements],
            [4, 3, 2]
        )

    def test_recent_movements_limited_to_eight(self):
        for number in range(12):
            self.create_movement(quantity=number + 1)

        movements = get_recent_stock_movements()

        self.assertEqual(len(movements), 8)
        self.assertEqual(movements[0].quantity_delta, 12)

    def test_inactive_products_and_suppliers_excluded(self):
        self.create_movement()

        inactive_product = Product.objects.create(
            sku='ACT-INACTIVE',
            name='Inactive Product',
            category='Test',
            supplier=self.supplier,
            unit_price=Decimal('50.00'),
            is_active=False
        )

        inactive_supplier_product = Product.objects.create(
            sku='ACT-SUPPLIER',
            name='Inactive Supplier Product',
            category='Test',
            supplier=self.inactive_supplier,
            unit_price=Decimal('50.00')
        )

        self.create_movement(inactive_product)
        self.create_movement(inactive_supplier_product)

        movements = get_recent_stock_movements()

        self.assertEqual(len(movements), 1)
        self.assertEqual(
            movements[0].product,
            self.product
        )

    def test_recent_sales_include_voided(self):
        self.create_sale('SALE-001')
        self.create_sale('SALE-002', status='voided')

        sales = get_recent_sales()

        self.assertEqual(
            [sale.sale_number for sale in sales],
            ['SALE-002', 'SALE-001']
        )
        self.assertEqual(sales[0].status, 'voided')

    def test_recent_sales_limited_to_five(self):
        for number in range(8):
            self.create_sale(f'SALE-{number:03d}')

        sales = get_recent_sales()

        self.assertEqual(len(sales), 5)
        self.assertEqual(sales[0].sale_number, 'SALE-007')

    def test_manager_sees_both_panels(self):
        self.create_movement(quantity=7)
        self.create_sale('SALE-MANAGER')

        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Recent Stock Movements')
        self.assertContains(response, 'Recent Sales')
        self.assertContains(response, 'Activity Product')
        self.assertContains(response, 'SALE-MANAGER')

    def test_voided_sale_label_visible(self):
        self.create_sale('SALE-VOIDED', status='voided')

        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertContains(response, 'SALE-VOIDED')
        self.assertContains(response, 'Voided')

    def test_sales_staff_cannot_see_manager_activity(self):
        self.create_movement()
        self.create_sale('SALE-PRIVATE')

        self.client.force_login(self.sales_user)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertNotContains(
            response,
            'Recent Stock Movements'
        )
        self.assertNotContains(
            response,
            'Recent Sales'
        )
        self.assertNotContains(response, 'SALE-PRIVATE')

    def test_admin_cannot_see_manager_activity(self):
        self.create_movement()
        self.create_sale('SALE-ADMIN-PRIVATE')

        self.client.force_login(self.admin)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertNotContains(
            response,
            'Recent Stock Movements'
        )
        self.assertNotContains(
            response,
            'Recent Sales'
        )

    def test_empty_messages(self):
        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'No stock movements recorded yet.'
        )
        self.assertContains(
            response,
            'No sales recorded yet.'
        )
```

### Code walkthrough
`get_recent_stock_movements()` reads `StockMovement` rows linked to active products and suppliers, preloads the product and creator with `select_related`, orders newest first and takes eight. The template shows the timestamp, product name, display label for the movement type, signed quantity change and username. Positive changes appear green and negative changes red. `get_recent_sales()` reads the five newest sales and preloads the creator. It deliberately does not filter out `voided` status, because history should retain cancelled transactions; the template marks them with a red Voided label. The view only loads these two panels for Managers, so Admin and Sales dashboards do not expose them.

### How we checked it
Ten Chapter 4 tests passed in **10.535 seconds**. The full suite passed **325 tests** in **414.175 seconds**, with zero Django system-check issues and no pending migrations. The staged whitespace check passed. Commit `4d331d8` was pushed to source `main` with four files. LF/CRLF warnings were informational.

### Where to go next
Previous: [Step 374](03-purchase-order-attention-panel.md#step-374). Next: Module 6 Chapter 5 — top-selling products and seven-day sales activity.

## Chapter summary
The Manager now sees eight recent stock movements and five recent sales, including voided transactions. No schema changes were needed. The project passes 325 tests.

**Recall question:** Why do recent sales include voided records while revenue summary calculations exclude them?

**Source:** [Chapter 4 source commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/4d331d8).
