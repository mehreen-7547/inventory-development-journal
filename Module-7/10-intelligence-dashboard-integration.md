# Module 7 — Chapter 10: Intelligence Dashboard Integration (Steps 488–496)

## Chapter introduction

The previous chapters produced four independent intelligence services but no manager-facing screen to run them. The existing role-based homepage already shows stock and sales summaries; this chapter adds a separate read-only analysis page for one selected active product. This avoids placing CPU-heavy forecasting in the homepage request.

**Source evidence:** [commit 9417c09](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/9417c09) on main, parent c5a8d43; six files (three new, three modified), 539 insertions and one deletion. **Tests:** 7/7 focused tests and 496/496 complete Django tests passed; system check zero issues, git diff --check passed. The first 7-test attempt failed because both fixture users lacked unique emails; corrected before source commit. No new migrations.

**Workflow:** Manager opens /intelligence/, selects an active product, submits GET ?product=ID. The view calls demand forecast, stock shortage, reorder advice and anomaly analysis independently, then renders results or unavailable messages. No stock changes, purchase orders, training or database writes.


## Step 488 — Inspect the existing interface before adding a new dashboard

**Before this step / Why.** Before this step, intelligence/views.py contained only Django's generated placeholder, and intelligence/urls.py did not exist. We inspected core/views.py, core/templates/core/home.html, catalog and inventory views/URLs and the role decorator. The manager homepage was already a populated role-specific dashboard, so a separate analysis page was selected rather than replacing it.

**Files:** `core/views.py`, `core/templates/core/home.html`, `accounts/decorators.py` (read-only inspection).

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 489.


## Step 489 — Confirm service dependencies and scope

**Before this step / Why.** The four services are not interchangeable: forecast requires a contiguous 60-day DailySales sequence and returns 30 days of P10/P50/P90; shortage uses that forecast plus on-hand and scheduled incoming stock; reorder uses AI with a 7-day-minimum non-AI fallback; anomalies use a 14-day rolling average. A product-specific, manager-only, read-only GET page was chosen, with independent failures.

**Files:** `intelligence/forecasting.py`, `intelligence/forecasting_data.py`, `intelligence/shortage_prediction.py`, `intelligence/reorder_recommendation.py`, `intelligence/anomaly_detection.py` (existing services).

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 490.


## Step 490 — Implement the manager-only intelligence view

**Before this step / Why.** Before this step no HTTP endpoint connected the services to a browser. The view obtains active products, reads a product ID from the query string, validates that it is numeric and belongs to an active product, then calls each service with the selected PK and current local date. Individual exception handling preserves other results if one analysis fails. This is a synchronous request and may take time when Chronos loads.

**Files:** `intelligence/views.py`.

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 491.


## Step 491 — Create a dedicated intelligence URL

**Before this step / Why.** The view needs a named route that templates and tests can reverse. The new intelligence/urls.py maps /intelligence/ to intelligence_dashboard, keeping this app's routing separate from other apps.

**Files:** `intelligence/urls.py`.

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 492.


## Step 492 — Connect the intelligence URLs to the project

**Before this step / Why.** Django's root URL configuration must include the new app's URL patterns. The existing admin, core, accounts, catalog, inventory, sales and purchasing routes remain intact.

**Files:** `config/urls.py`.

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 493.


## Step 493 — Create the read-only dashboard template

**Before this step / Why.** The new template extends base.html, uses a GET product selector, and displays four independent result panels: 30-day P10/P50/P90 forecast table, first safety breach/stockout and incoming warnings, reorder quantity/method/reason, and anomaly counts and explanations. Unavailable analyses show messages; there are no mutation forms or order buttons. It explicitly labels estimates as decision support.

**Files:** `intelligence/templates/intelligence/dashboard.html`.

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 494.


## Step 494 — Add the manager homepage navigation link

**Before this step / Why.** The existing manager homepage already had links to suppliers, products, purchasing and sales. A Demand Intelligence link was inserted immediately below the Inventory Manager Dashboard heading. It uses the named URL, so routing can change without hardcoding the address.

**Files:** `core/templates/core/home.html`.

**How we checked it.** The resulting behavior is exercised by the seven dashboard tests and the full 496-test suite after integration.

**Next:** Step 495.


## Step 495 — Test authorization, selection, rendering and partial failure

**Before this step / Why.** Seven Django TestCase tests cover anonymous login redirect, sales-user denial, manager page access, invalid and inactive product rejection, all four mocked results and independent failure handling. The first run had seven setUp errors: both test accounts used the default blank email but CustomUser requires email uniqueness. Distinct test emails fixed the fixture; the corrected 7/7 passed in 6.429 seconds. The complete 496/496 suite passed in 647.366 seconds.

**Files:** `intelligence/test_dashboard.py`.

**How we checked it.** The final seven targeted tests and 496 full tests passed.

**Next:** Step 496.


## Step 496 — Commit and publish the verified source

**Before this step / Why.** After all 496 tests passed and git diff --check showed no whitespace errors, the six intended files were staged explicitly and committed as 9417c09, then pushed to origin/main. Git's LF/CRLF conversion warnings were non-fatal. The unrelated untracked purchasing.zip and reset_business_data_once.py were not staged.

**Files:** Git source commit `9417c09`.

**How we checked it.** The user-provided git commit/push output confirms source commit 9417c09 on main.

**Next:** Chapter review and historical-data demonstration.


## Committed code — exact source files

These are the actual contents from source main after commit 9417c09. The manager homepage is long, so its precise inserted excerpt is shown separately.


### intelligence/views.py — MODIFIED

```python

from django.shortcuts import render
from django.utils import timezone

from accounts.decorators import role_required
from catalog.models import Product

from .forecasting import forecast_product_demand
from .shortage_prediction import predict_stock_shortage
from .reorder_recommendation import recommend_reorder_quantity
from .anomaly_detection import detect_sales_anomalies


@role_required('manager')
def intelligence_dashboard(request):
    products = Product.objects.filter(
        is_active=True
    ).order_by('name')

    selected_product = None
    results = {}
    errors = {}

    product_id = request.GET.get('product')

    if product_id:
        selected_product = products.filter(
            pk=product_id
        ).first() if product_id.isdigit() else None

        if selected_product is None:
            errors['selection'] = (
                'Please select a valid active product.'
            )
        else:
            end_date = timezone.localdate()

            services = {
                'forecast': forecast_product_demand,
                'shortage': predict_stock_shortage,
                'reorder': recommend_reorder_quantity,
                'anomalies': detect_sales_anomalies
            }

            for name, service in services.items():
                try:
                    results[name] = service(
                        selected_product.pk,
                        end_date
                    )
                except Exception:
                    errors[name] = (
                        'This analysis is currently unavailable.'
                    )

    return render(
        request,
        'intelligence/dashboard.html',
        {
            'products': products,
            'selected_product': selected_product,
            'results': results,
            'errors': errors
        }
    )
```


### intelligence/urls.py — NEW

```python

from django.urls import path
from . import views


urlpatterns = [
    path(
        'intelligence/',
        views.intelligence_dashboard,
        name='intelligence_dashboard'
    ),
]
```


### config/urls.py — MODIFIED

```python
from django.contrib import admin
from django.urls import include, path


urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("core.urls")),
    path("", include("accounts.urls")),
    path('', include('catalog.urls')),
    path('', include('inventory.urls')),
    path('', include('sales.urls')),
    path('', include('purchasing.urls')),
    path('', include('intelligence.urls')),
]
```


### intelligence/templates/intelligence/dashboard.html — NEW

```html

{% extends 'base.html' %}

{% block title %}Demand Intelligence{% endblock %}

{% block content %}

<h2 class="mb-3">Demand Intelligence</h2>

<p class="text-secondary">
    Select a product to view its demand and stock analysis.
</p>

<form method="get" class="mb-4">
    <label for="product" class="form-label">
        Product
    </label>

    <select
        name="product"
        id="product"
        class="form-select mb-3"
        required
    >
        <option value="">Select a product</option>

        {% for product in products %}
        <option
            value="{{ product.pk }}"
            {% if selected_product.pk == product.pk %}
            selected
            {% endif %}
        >
            {{ product.name }} ({{ product.sku }})
        </option>
        {% endfor %}
    </select>

    <button type="submit" class="btn btn-primary">
        Analyze Product
    </button>
</form>

{% if errors.selection %}
<div class="alert alert-warning">
    {{ errors.selection }}
</div>
{% endif %}

{% if selected_product %}

<h3 class="h5 mb-3">
    Analysis: {{ selected_product.name }}
</h3>

<p class="text-secondary">
    Current stock: {{ selected_product.on_hand }}
    | Safety stock: {{ selected_product.safety_stock }}
</p>

<div class="row g-3">

    <div class="col-md-6">
        <div class="card h-100">
            <div class="card-body">
                <h4 class="h6">Demand Forecast</h4>

                {% if results.forecast %}
                    <p>
                        Forecast period:
                        {{ results.forecast.forecast_days }} days
                    </p>

                    <p>
                        Model: {{ results.forecast.model }}
                    </p>

                    <div class="table-responsive">
                        <table class="table table-sm">
                            <thead>
                                <tr>
                                    <th>Date</th>
                                    <th>Low</th>
                                    <th>Expected</th>
                                    <th>High</th>
                                </tr>
                            </thead>
                            <tbody>
                                {% for day in results.forecast.predictions %}
                                <tr>
                                    <td>{{ day.date }}</td>
                                    <td>{{ day.p10 }}</td>
                                    <td>{{ day.p50 }}</td>
                                    <td>{{ day.p90 }}</td>
                                </tr>
                                {% endfor %}
                            </tbody>
                        </table>
                    </div>
                {% else %}
                    <p class="text-secondary">
                        {{ errors.forecast|default:"No forecast available." }}
                    </p>
                {% endif %}
            </div>
        </div>
    </div>

    <div class="col-md-6">
        <div class="card h-100">
            <div class="card-body">
                <h4 class="h6">Stock-Shortage Prediction</h4>

                {% if results.shortage %}
                    <p>
                        First safety-stock breach:
                        {{ results.shortage.first_safety_breach|default:"None predicted" }}
                    </p>

                    <p>
                        First stockout:
                        {{ results.shortage.first_stockout|default:"None predicted" }}
                    </p>

                    <p>
                        Overdue incoming:
                        {{ results.shortage.overdue_incoming }}
                    </p>

                    <p>
                        Unscheduled incoming:
                        {{ results.shortage.unscheduled_incoming }}
                    </p>
                {% else %}
                    <p class="text-secondary">
                        {{ errors.shortage|default:"No shortage prediction available." }}
                    </p>
                {% endif %}
            </div>
        </div>
    </div>

    <div class="col-md-6">
        <div class="card h-100">
            <div class="card-body">
                <h4 class="h6">Reorder Recommendation</h4>

                {% if results.reorder %}
                    {% if results.reorder.status == 'available' %}
                        <p>
                            Recommended quantity:
                            <strong>
                                {{ results.reorder.reorder_quantity }}
                            </strong>
                        </p>

                        <p>
                            Method: {{ results.reorder.method }}
                        </p>

                        <p>{{ results.reorder.reason }}</p>

                        <p>
                            Scheduled incoming:
                            {{ results.reorder.scheduled_incoming }}
                        </p>
                    {% else %}
                        <p class="text-secondary">
                            {{ results.reorder.reason }}
                        </p>
                    {% endif %}
                {% else %}
                    <p class="text-secondary">
                        {{ errors.reorder|default:"No recommendation available." }}
                    </p>
                {% endif %}
            </div>
        </div>
    </div>

    <div class="col-md-6">
        <div class="card h-100">
            <div class="card-body">
                <h4 class="h6">Unusual Sales Patterns</h4>

                {% if results.anomalies %}
                    <p>
                        Anomalies detected:
                        {{ results.anomalies.anomaly_count }}
                    </p>

                    <p>
                        Days analyzed:
                        {{ results.anomalies.analyzed_days }}
                    </p>

                    {% for anomaly in results.anomalies.anomalies %}
                        <div class="border-bottom mb-2 pb-2">
                            <strong>{{ anomaly.date }}</strong>

                            <p class="mb-1">
                                {{ anomaly.explanation }}
                            </p>

                            <small>
                                Actual: {{ anomaly.actual_sales }}
                                | Average: {{ anomaly.average_sales }}
                            </small>
                        </div>
                    {% empty %}
                        <p>No unusual sales patterns detected.</p>
                    {% endfor %}

                    {% if results.anomalies.skipped_days %}
                        <p class="text-secondary small">
                            Some days could not be analyzed
                            because sales history was incomplete.
                        </p>
                    {% endif %}
                {% else %}
                    <p class="text-secondary">
                        {{ errors.anomalies|default:"No anomaly analysis available." }}
                    </p>
                {% endif %}
            </div>
        </div>
    </div>

</div>

<p class="text-secondary small mt-3">
    These are decision-support estimates.
    No purchase orders or stock adjustments are made automatically.
</p>

{% endif %}

<a href="{% url 'home' %}"
   class="btn btn-outline-secondary mt-3">
    Back to Dashboard
</a>

{% endblock %}
```


### intelligence/test_dashboard.py — NEW, TEST CLASS

```python

from decimal import Decimal
from unittest.mock import patch

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from catalog.models import Product, Supplier


class IntelligenceDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.manager = User.objects.create_user(
            username='intelligence_manager',
            email='intelligence_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='intelligence_sales',
            email='intelligence_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.supplier = Supplier.objects.create(
            name='Dashboard Supplier'
        )

        self.product = Product.objects.create(
            sku='INT-001',
            name='Dashboard Notebook',
            category='Stationery',
            supplier=self.supplier,
            unit_price=Decimal('50.00'),
            lead_time_days=7,
            safety_stock=10
        )

        self.url = reverse('intelligence_dashboard')

    def test_login_required(self):
        response = self.client.get(self.url)

        self.assertEqual(response.status_code, 302)
        self.assertIn('/login/', response.url)

    def test_sales_user_cannot_access(self):
        self.client.force_login(self.sales_user)

        response = self.client.get(self.url)

        self.assertRedirects(
            response,
            reverse('home'),
            fetch_redirect_response=False
        )

    def test_manager_can_open_dashboard(self):
        self.client.force_login(self.manager)

        response = self.client.get(self.url)

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Demand Intelligence')
        self.assertContains(response, self.product.name)

    def test_invalid_product_is_rejected(self):
        self.client.force_login(self.manager)

        response = self.client.get(
            self.url,
            {'product': 'invalid'}
        )

        self.assertContains(
            response,
            'Please select a valid active product.'
        )

    def test_inactive_product_is_rejected(self):
        self.product.is_active = False
        self.product.save(update_fields=['is_active'])

        self.client.force_login(self.manager)

        response = self.client.get(
            self.url,
            {'product': self.product.pk}
        )

        self.assertContains(
            response,
            'Please select a valid active product.'
        )

    @patch('intelligence.views.detect_sales_anomalies')
    @patch('intelligence.views.recommend_reorder_quantity')
    @patch('intelligence.views.predict_stock_shortage')
    @patch('intelligence.views.forecast_product_demand')
    def test_all_results_are_displayed(
        self,
        forecast,
        shortage,
        reorder,
        anomalies
    ):
        forecast.return_value = {
            'forecast_days': 30,
            'model': 'Test Model',
            'predictions': []
        }

        shortage.return_value = {
            'first_safety_breach': None,
            'first_stockout': None,
            'overdue_incoming': 0,
            'unscheduled_incoming': 0
        }

        reorder.return_value = {
            'status': 'available',
            'reorder_quantity': 15,
            'method': 'ai_chronos',
            'reason': 'Reorder recommended',
            'scheduled_incoming': 0
        }

        anomalies.return_value = {
            'anomaly_count': 0,
            'analyzed_days': 30,
            'anomalies': [],
            'skipped_days': []
        }

        self.client.force_login(self.manager)

        response = self.client.get(
            self.url,
            {'product': self.product.pk}
        )

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Model')
        self.assertContains(response, 'Reorder recommended')
        self.assertContains(
            response,
            'No unusual sales patterns detected.'
        )

        forecast.assert_called_once()
        shortage.assert_called_once()
        reorder.assert_called_once()
        anomalies.assert_called_once()

    @patch('intelligence.views.detect_sales_anomalies')
    @patch('intelligence.views.recommend_reorder_quantity')
    @patch('intelligence.views.predict_stock_shortage')
    @patch('intelligence.views.forecast_product_demand')
    def test_forecast_failure_does_not_hide_other_results(
        self,
        forecast,
        shortage,
        reorder,
        anomalies
    ):
        forecast.side_effect = ValueError(
            'Missing history'
        )

        shortage.side_effect = ValueError(
            'Missing history'
        )

        reorder.return_value = {
            'status': 'available',
            'reorder_quantity': 8,
            'method': 'non_ai_fallback',
            'reason': 'Reorder recommended',
            'scheduled_incoming': 0
        }

        anomalies.return_value = {
            'anomaly_count': 0,
            'analyzed_days': 1,
            'anomalies': [],
            'skipped_days': []
        }

        self.client.force_login(self.manager)

        response = self.client.get(
            self.url,
            {'product': self.product.pk}
        )

        self.assertEqual(response.status_code, 200)
        self.assertContains(
            response,
            'This analysis is currently unavailable.'
        )
        self.assertContains(
            response,
            'non_ai_fallback'
        )
        self.assertContains(
            response,
            'No unusual sales patterns detected.'
        )
```


### core/templates/core/home.html — MODIFIED

```html
iv>
        </div>


        {% elif request.user.role == 'manager' %}

        <h2 class="h5 mb-3">Inventory Manager Dashboard</h2>

        <a href="{% url 'intelligence_dashboard' %}"
        class="btn btn-outline-primary mb-3">
            Demand Intelligence
        </a>

        <div class="d-flex flex-wrap gap-2">
            <a href="{% url 'supplier_list' %}" class="btn btn-outline-primary">
                Manage Suppliers
            </a>

            <a href="{% url 'product_list' %
```


## Code explanation and review

**View and role decorator.** `@role_required('manager')` wraps the view and redirects users without the manager role; this is access control on the server, not merely hiding a link. `Product.objects.filter(is_active=True)` limits selection to active products. The query parameter is validated before selecting a product. The `services` dictionary pairs a panel key with a callable, and the loop stores either a result or an unavailable message for that key. The exception handler does not reveal stack traces to the browser, but it also does not log them; adding logging is a future operational improvement.

**URLs.** `intelligence/urls.py` declares a named path. `config/urls.py` includes it. `{% url 'intelligence_dashboard' %}` in the manager homepage resolves the name rather than repeating a literal path.

**Template.** The page extends the shared Bootstrap base. The GET selector reloads the page with the selected product ID. Each panel checks its own result; forecast rows are a 30-day table, not a chart. The anomaly panel displays explanations from the statistical detector. No database mutations occur on page render.

**Tests.** `setUp` creates a supplier, a product, and two users with distinct email addresses. `force_login` avoids relying on login-form details. `patch` replaces the four expensive services so tests check the view and HTML without loading Chronos. The error-path test proves that one service failure does not hide other panels. These are interface tests, not evidence of live model accuracy.

**Manual observation after deployment.** The user opened the dashboard and found forecasting/shortage unavailable and reorder reporting insufficient history; anomaly processing showed skipped days. No CSV history had been imported into the active local database. This is not a failed automated test; it means the runtime data requirements are unmet. A CSV import service already exists in intelligence/services.py, but no browser upload page was added in this chapter.

**Limits and caution.** The four services are called separately; forecast inference may repeat within one page request (although model weights are cached). Broad exception handling hides the exact failure reason. The current system uses the last 60 consecutive recorded/imported DailySales days as context and forecasts the next 30 days. The model is pretrained, not fine-tuned. Historical CSV data is a daily summary and must not be confused with stock movements or real completed sales.

**Review questions.** Why is the intelligence page manager-only? Why use a GET query string rather than a POST? Why do we require active products? What is the difference between a missing daily row and a row with zero units? What happens if Chronos cannot load? Which tests use mocks, and what would a real end-to-end model probe verify? Why does the CSV import not alter on-hand inventory?
