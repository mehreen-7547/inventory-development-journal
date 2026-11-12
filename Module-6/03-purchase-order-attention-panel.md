# Module 6 — Chapter 3: Purchase Orders Needing Attention

## Before we begin
Chapter 2 displayed stock shortages. Managers also need to identify outstanding supplier deliveries. This chapter adds a read-only list of the five open purchase orders with the earliest expected dates, prioritizing overdue deliveries.

## What we worked on
We added a query in `core/dashboard.py`, passed its results from the existing Manager home view, rendered a linked Bootstrap table and wrote seven focused tests. No new tables, model fields, apps or dependencies were needed.

## Picture it this way
The open-order summary card gives a count. This panel shows which supplier orders should be checked first, how late they are, and a link to the existing order detail page.

[Step 374](#step-374) · [Chapter summary](#chapter-summary)

## Step 374 — Show five purchase orders needing attention

### Before this step
The Manager dashboard had six cards and a low-stock product list, but no purchase-order follow-up list. The approved scope requires open orders only, overdue orders first, supplier, expected date, status, and days overdue.

### What we did and why
Added a separate query to retrieve the five earliest open orders from active suppliers. It computes days overdue in Python from the local date and returns both the order and computed value. The view exposes this information only to Managers. The template links the PO number to its existing detail route, marks overdue rows red, and displays a one-line message when no orders qualify.

### Code
**CLASS — `core/dashboard.py`** (new committed function):

```python
def get_purchase_orders_needing_attention():
    today = timezone.localdate()

    orders = PurchaseOrder.objects.filter(
        supplier__is_active=True,
        status__in=['ordered', 'partially_received']
    ).select_related('supplier').order_by(
        F('expected_delivery_date').asc(nulls_last=True),
        'created_at',
        'pk'
    )[:5]

    results = []

    for order in orders:
        days_overdue = 0

        if (
            order.expected_delivery_date
            and order.expected_delivery_date < today
        ):
            days_overdue = (
                today - order.expected_delivery_date
            ).days

        results.append({
            'order': order,
            'days_overdue': days_overdue
        })

    return results
```

**CLASS — `core/views.py`** (complete committed file):

```python
from django.shortcuts import render

from accounts.decorators import role_required
from .dashboard import (
    get_manager_summary,
    get_low_stock_products,
    get_purchase_orders_needing_attention
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

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (new purchase-order panel):

```django
<div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Purchase Orders Needing Attention
                </h3>
            </div>

            <div class="card-body">
                {% if purchase_orders_attention %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>PO Number</th>
                                <th>Supplier</th>
                                <th>Expected Delivery</th>
                                <th>Status</th>
                                <th>Days Overdue</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for item in purchase_orders_attention %}
                            <tr class="{% if item.days_overdue > 0 %}table-danger{% endif %}">
                                <td>
                                    <a href="{% url 'purchase_order_detail' item.order.pk %}">
                                        {{ item.order.po_number }}
                                    </a>
                                </td>
                                <td>{{ item.order.supplier.name }}</td>
                                <td>
                                    {% if item.order.expected_delivery_date %}
                                        {{ item.order.expected_delivery_date|date:"d M Y" }}
                                    {% else %}
                                        Not set
                                    {% endif %}
                                </td>
                                <td>{{ item.order.get_status_display }}</td>
                                <td>
                                    {% if item.days_overdue > 0 %}
                                        {{ item.days_overdue }}
                                    {% else %}
                                        -
                                    {% endif %}
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No open purchase orders need attention.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_purchase_order_dashboard.py`** (complete committed file):

```python
from datetime import timedelta

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse
from django.utils import timezone

from catalog.models import Supplier
from purchasing.models import PurchaseOrder
from core.dashboard import get_purchase_orders_needing_attention


class PurchaseOrderDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='po_dashboard_manager',
            email='po_dashboard_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='po_dashboard_sales',
            email='po_dashboard_sales@example.com',
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

        self.today = timezone.localdate()

    def create_order(
        self, number, days_from_today=None,
        status='ordered', supplier=None
    ):
        expected_date = None

        if days_from_today is not None:
            expected_date = (
                self.today
                + timedelta(days=days_from_today)
            )

        return PurchaseOrder.objects.create(
            po_number=number,
            supplier=supplier or self.supplier,
            status=status,
            expected_delivery_date=expected_date,
            created_by=self.manager
        )

    def test_only_open_orders_from_active_suppliers(self):
        self.create_order('PO-OPEN', -2)
        self.create_order(
            'PO-PARTIAL', -1,
            status='partially_received'
        )
        self.create_order('PO-DRAFT', -5, status='draft')
        self.create_order('PO-RECEIVED', -4, status='received')
        self.create_order('PO-CANCELLED', -3, status='cancelled')
        self.create_order(
            'PO-INACTIVE', -6,
            supplier=self.inactive_supplier
        )

        results = get_purchase_orders_needing_attention()
        numbers = [item['order'].po_number for item in results]

        self.assertEqual(
            numbers,
            ['PO-OPEN', 'PO-PARTIAL']
        )

    def test_overdue_orders_appear_first(self):
        self.create_order('PO-FUTURE', 3)
        self.create_order('PO-OLDER', -5)
        self.create_order('PO-RECENT', -2)
        self.create_order('PO-NO-DATE')

        results = get_purchase_orders_needing_attention()

        self.assertEqual(
            [item['order'].po_number for item in results],
            [
                'PO-OLDER',
                'PO-RECENT',
                'PO-FUTURE',
                'PO-NO-DATE'
            ]
        )

    def test_days_overdue(self):
        self.create_order('PO-LATE', -4)
        self.create_order('PO-TODAY', 0)
        self.create_order('PO-FUTURE', 2)

        results = get_purchase_orders_needing_attention()

        days = {
            item['order'].po_number: item['days_overdue']
            for item in results
        }

        self.assertEqual(days['PO-LATE'], 4)
        self.assertEqual(days['PO-TODAY'], 0)
        self.assertEqual(days['PO-FUTURE'], 0)

    def test_limit_to_five_orders(self):
        for number in range(8):
            self.create_order(
                f'PO-{number:03d}',
                -(number + 1)
            )

        results = get_purchase_orders_needing_attention()

        self.assertEqual(len(results), 5)
        self.assertEqual(
            results[0]['order'].po_number,
            'PO-007'
        )

    def test_manager_sees_panel_and_detail_link(self):
        order = self.create_order('PO-ATTENTION', -3)

        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertContains(
            response,
            'Purchase Orders Needing Attention'
        )
        self.assertContains(response, 'PO-ATTENTION')
        self.assertContains(
            response,
            reverse('purchase_order_detail', args=[order.pk])
        )
        self.assertContains(response, 'table-danger')

    def test_sales_staff_cannot_see_panel(self):
        self.create_order('PO-PRIVATE', -3)

        self.client.force_login(self.sales_user)
        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)
        self.assertNotContains(
            response,
            'Purchase Orders Needing Attention'
        )
        self.assertNotContains(response, 'PO-PRIVATE')

    def test_empty_panel_message(self):
        self.client.force_login(self.manager)
        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'No open purchase orders need attention.'
        )
```

### Code walkthrough
The query restricts orders to `ordered` or `partially_received` and requires an active supplier. `select_related('supplier')` avoids repeatedly fetching the supplier for each row. Ordering by `expected_delivery_date` ascending, with null dates last, puts older overdue dates before current or future dates. The remaining sort keys make ties stable. The slice limits results to five. Each order is wrapped with `days_overdue`, calculated as the difference between today and the expected date when that date has passed; otherwise the value is zero. The template shows a red row only when overdue, and uses the existing named `purchase_order_detail` URL. The tests verify filtering, order, date arithmetic, five-row limit, links, role isolation and empty states.

### How we checked it
The full Django suite passed **315 tests** in **379.849 seconds** with zero system-check issues. `makemigrations --check --dry-run` reported no changes. Git's staged whitespace check passed. Source commit `698c81c` was pushed to `main`, changing exactly four files; the LF/CRLF warnings were informational.

### Where to go next
Previous: [Step 373](02-low-stock-products-panel.md#step-373). Next: Module 6 Chapter 4 — recent stock movements and sales.

## Chapter summary
The Manager dashboard now lists up to five open purchase orders, sorted by earliest expected delivery, with overdue highlighting and links to order details. The feature was verified by seven new tests and the 315-test project suite.

**Recall question:** Why does the query place purchase orders with no expected delivery date last?

**Source:** [Chapter 3 source commit](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/698c81c).
