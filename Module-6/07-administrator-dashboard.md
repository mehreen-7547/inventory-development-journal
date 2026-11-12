# Module 6 — Chapter 7: Administrator Dashboard

## Before this chapter
Chapters 1–5 built the Manager dashboard and Chapter 6 added the Sales Staff dashboard. The Administrator previously had employee-management links but no overview of account status and password setup. This chapter adds a read-only account dashboard using the existing `CustomUser` model and email setup route, without new apps, dependencies or database migrations.

## What we implemented
Four account-summary cards now report total active accounts, active accounts by Admin/Manager/Sales role, deactivated accounts, and active employees still awaiting a password. Two tables show all eligible pending-setup employees with a secure resend action and the five most recently created accounts. The Manager and Sales dashboards remain isolated from this data.

## Step 378 — Administrator account overview

### Before this step
Admins could navigate to employee management, create or edit staff accounts and resend a setup email from the employee screens. They had no centralized account counts or visibility of pending password setup on the home page.

### What we did and why
We reused `CustomUser` and `UNUSABLE_PASSWORD_PREFIX` rather than adding a setup-state field. Django prefixes unusable password hashes with this marker. Only **active Manager and Sales Staff accounts** are eligible for the pending-setup table; the existing email resend endpoint deliberately rejects inactive employees and excludes Admin accounts. Recent users includes all roles and statuses so an Administrator can see newly created and subsequently deactivated accounts.

### Code

**CLASS — `core/dashboard.py`** (new imports; preserve existing imports and functions):

```python
from django.contrib.auth.hashers import UNUSABLE_PASSWORD_PREFIX
from accounts.models import CustomUser
```

**CLASS — `core/dashboard.py`** (the three newly added functions, from source commit `ccc99f3`):

```python
def get_admin_summary():
    active_users = CustomUser.objects.filter(
        is_active=True
    )

    pending_setup = active_users.filter(
        role__in=['manager', 'sales'],
        password__startswith=UNUSABLE_PASSWORD_PREFIX
    )

    return {
        'active_count': active_users.count(),
        'active_admin_count': active_users.filter(
            role='admin'
        ).count(),
        'active_manager_count': active_users.filter(
            role='manager'
        ).count(),
        'active_sales_count': active_users.filter(
            role='sales'
        ).count(),
        'inactive_count': CustomUser.objects.filter(
            is_active=False
        ).count(),
        'pending_setup_count': pending_setup.count()
    }


def get_pending_password_setup():
    return list(
        CustomUser.objects.filter(
            is_active=True,
            role__in=['manager', 'sales'],
            password__startswith=UNUSABLE_PASSWORD_PREFIX
        ).order_by(
            'date_joined',
            'pk'
        )
    )


def get_recent_users():
    return list(
        CustomUser.objects.order_by(
            '-date_joined',
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
    get_recent_sales,
    get_top_selling_products,
    get_daily_sales_last_seven_days,
    get_sales_staff_summary,
    get_sales_staff_recent_sales,
    get_sales_staff_stock_alerts,
    get_admin_summary,
    get_pending_password_setup,
    get_recent_users
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

    elif request.user.role == 'admin':
        context['admin_summary'] = get_admin_summary()

        context['pending_password_setup'] = (
            get_pending_password_setup()
        )

        context['recent_users'] = get_recent_users()

    return render(request, 'core/home.html', context)
```

**CLASS — `core/templates/core/home.html`** (new Admin content below existing navigation buttons):

```django
<div class="row g-3 mt-3">

            <div class="col-md-3 col-sm-6">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Active Accounts</h3>
                        <p class="h3 mb-0">
                            {{ admin_summary.active_count }}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-3 col-sm-6">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Active Accounts by Role</h3>
                        <p class="small mb-1">
                            Admin: {{ admin_summary.active_admin_count }}
                        </p>
                        <p class="small mb-1">
                            Manager: {{ admin_summary.active_manager_count }}
                        </p>
                        <p class="small mb-0">
                            Sales: {{ admin_summary.active_sales_count }}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-3 col-sm-6">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Deactivated Accounts</h3>
                        <p class="h3 mb-0">
                            {{ admin_summary.inactive_count }}
                        </p>
                    </div>
                </div>
            </div>

            <div class="col-md-3 col-sm-6">
                <div class="card h-100">
                    <div class="card-body">
                        <h3 class="h6">Pending Password Setup</h3>
                        <p class="h3 mb-0">
                            {{ admin_summary.pending_setup_count }}
                        </p>
                    </div>
                </div>
            </div>

        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Employees Awaiting Password Setup
                </h3>
            </div>

            <div class="card-body">
                {% if pending_password_setup %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Username</th>
                                <th>Email</th>
                                <th>Role</th>
                                <th>Action</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for employee in pending_password_setup %}
                            <tr>
                                <td>{{ employee.username }}</td>
                                <td>{{ employee.email }}</td>
                                <td>
                                    {{ employee.get_role_display }}
                                </td>
                                <td>
                                    <form
                                        method="post"
                                        action="{% url 'resend_setup_email' employee.pk %}"
                                    >
                                        {% csrf_token %}
                                        <button
                                            type="submit"
                                            class="btn btn-outline-primary btn-sm"
                                        >
                                            Resend Setup Email
                                        </button>
                                    </form>
                                </td>
                            </tr>
                            {% endfor %}
                        </tbody>
                    </table>
                </div>
                {% else %}
                <p class="text-secondary mb-0">
                    No active employees are awaiting password setup.
                </p>
                {% endif %}
            </div>
        </div>

        <div class="card mt-4">
            <div class="card-header bg-white">
                <h3 class="h5 mb-0">
                    Recently Added Accounts
                </h3>
            </div>

            <div class="card-body">
                {% if recent_users %}
                <div class="table-responsive">
                    <table class="table table-bordered table-hover align-middle">
                        <thead>
                            <tr>
                                <th>Username</th>
                                <th>Role</th>
                                <th>Date Joined</th>
                                <th>Status</th>
                            </tr>
                        </thead>

                        <tbody>
                            {% for employee in recent_users %}
                            <tr>
                                <td>{{ employee.username }}</td>
                                <td>
                                    {{ employee.get_role_display }}
                                </td>
                                <td>
                                    {{ employee.date_joined|date:"d M Y, h:i A" }}
                                </td>
                                <td>
                                    {% if employee.is_active %}
                                    <span class="badge bg-success">
                                        Active
                                    </span>
                                    {% else %}
                                    <span class="badge bg-secondary">
                                        Deactivated
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
                    No accounts found.
                </p>
                {% endif %}
            </div>
        </div>
```

**NEW — `core/test_admin_dashboard.py`** (complete committed file, 13 tests):

```python
from unittest.mock import patch

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from core.dashboard import (
    get_admin_summary,
    get_pending_password_setup,
    get_recent_users
)


class AdminDashboardTests(TestCase):

    def setUp(self):
        User = get_user_model()

        self.admin = User.objects.create_user(
            username='dashboard_admin',
            email='dashboard_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        self.manager = User.objects.create_user(
            username='dashboard_manager_admin_test',
            email='dashboard_manager_admin@example.com',
            password='TestPassword123!',
            role='manager'
        )

        self.sales_user = User.objects.create_user(
            username='dashboard_sales_admin_test',
            email='dashboard_sales_admin@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.pending = User.objects.create_user(
            username='pending_employee',
            email='pending_employee@example.com',
            password='TestPassword123!',
            role='sales'
        )

        self.pending.set_unusable_password()
        self.pending.save(update_fields=['password'])

        self.inactive = User.objects.create_user(
            username='inactive_employee',
            email='inactive_employee@example.com',
            password='TestPassword123!',
            role='sales',
            is_active=False
        )

        self.inactive_pending = User.objects.create_user(
            username='inactive_pending',
            email='inactive_pending@example.com',
            password='TestPassword123!',
            role='manager',
            is_active=False
        )

        self.inactive_pending.set_unusable_password()
        self.inactive_pending.save(update_fields=['password'])

    def test_summary_counts_active_and_deactivated(self):
        summary = get_admin_summary()

        self.assertEqual(summary['active_count'], 4)
        self.assertEqual(summary['inactive_count'], 2)

    def test_active_role_counts(self):
        summary = get_admin_summary()

        self.assertEqual(summary['active_admin_count'], 1)
        self.assertEqual(summary['active_manager_count'], 1)
        self.assertEqual(summary['active_sales_count'], 2)

    def test_pending_only_active_employees(self):
        summary = get_admin_summary()
        pending = get_pending_password_setup()

        self.assertEqual(
            summary['pending_setup_count'],
            1
        )

        self.assertEqual(
            [employee.username for employee in pending],
            ['pending_employee']
        )

        self.assertFalse(
            pending[0].has_usable_password()
        )

    def test_pending_excludes_admin_accounts(self):
        User = get_user_model()

        another_admin = User.objects.create_user(
            username='unusable_admin',
            email='unusable_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        another_admin.set_unusable_password()
        another_admin.save(update_fields=['password'])

        pending = get_pending_password_setup()
        summary = get_admin_summary()

        self.assertEqual(len(pending), 1)
        self.assertEqual(
            summary['pending_setup_count'],
            1
        )

        self.assertNotIn(
            another_admin,
            pending
        )

    def test_password_setup_removes_pending_employee(self):
        self.pending.set_password('NewPassword123!')
        self.pending.save(update_fields=['password'])

        summary = get_admin_summary()
        pending = get_pending_password_setup()

        self.assertEqual(
            summary['pending_setup_count'],
            0
        )

        self.assertEqual(pending, [])

    def test_recent_users_limited_to_five(self):
        recent = get_recent_users()

        self.assertEqual(len(recent), 5)

        self.assertEqual(
            recent[0].username,
            'inactive_pending'
        )

        self.assertNotIn(self.admin, recent)

    def test_admin_dashboard_displays_account_information(self):
        self.client.force_login(self.admin)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertContains(
            response,
            'Active Accounts'
        )

        self.assertContains(
            response,
            'Active Accounts by Role'
        )

        self.assertContains(
            response,
            'Deactivated Accounts'
        )

        self.assertContains(
            response,
            'Pending Password Setup'
        )

        self.assertContains(
            response,
            'Employees Awaiting Password Setup'
        )

        self.assertContains(
            response,
            'Recently Added Accounts'
        )

        self.assertContains(
            response,
            'pending_employee'
        )

        self.assertContains(
            response,
            'Resend Setup Email'
        )

        self.assertNotContains(
            response,
            'Top-selling Products'
        )

        self.assertNotContains(
            response,
            'My Sales Today'
        )

        self.assertNotContains(
            response,
            'Purchase Orders Needing Attention'
        )

        self.assertNotIn(
            'manager_summary',
            response.context
        )

        self.assertNotIn(
            'sales_staff_summary',
            response.context
        )

    @patch('accounts.views.send_welcome_email')
    def test_pending_resend_action_uses_existing_route(
        self, mock_email
    ):
        self.client.force_login(self.admin)

        url = reverse(
            'resend_setup_email',
            args=[self.pending.pk]
        )

        response = self.client.get(reverse('home'))

        self.assertContains(response, url)

        response = self.client.post(url)

        self.assertRedirects(
            response,
            reverse('employee_list')
        )

        mock_email.assert_called_once()

        self.assertEqual(
            mock_email.call_args.args[1].pk,
            self.pending.pk
        )

    def test_resend_email_requires_post(self):
        self.client.force_login(self.admin)

        url = reverse(
            'resend_setup_email',
            args=[self.pending.pk]
        )

        response = self.client.get(url)

        self.assertEqual(response.status_code, 405)

    def test_manager_cannot_see_admin_dashboard(self):
        self.client.force_login(self.manager)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertNotContains(
            response,
            'Employees Awaiting Password Setup'
        )

        self.assertNotContains(
            response,
            'Recently Added Accounts'
        )

        self.assertNotIn(
            'admin_summary',
            response.context
        )

        self.assertNotIn(
            'pending_password_setup',
            response.context
        )

    def test_sales_cannot_see_admin_dashboard(self):
        self.client.force_login(self.sales_user)

        response = self.client.get(reverse('home'))

        self.assertEqual(response.status_code, 200)

        self.assertNotContains(
            response,
            'Employees Awaiting Password Setup'
        )

        self.assertNotContains(
            response,
            'Recently Added Accounts'
        )

        self.assertNotContains(
            response,
            'pending_employee'
        )

        self.assertNotIn(
            'admin_summary',
            response.context
        )

        self.assertNotIn(
            'recent_users',
            response.context
        )

    def test_empty_pending_setup_message(self):
        self.pending.set_password('NewPassword123!')
        self.pending.save(update_fields=['password'])

        self.client.force_login(self.admin)

        response = self.client.get(reverse('home'))

        self.assertContains(
            response,
            'No active employees are awaiting password setup.'
        )

        self.assertNotContains(
            response,
            'Resend Setup Email'
        )

    def test_inactive_employee_has_no_resend_action(self):
        self.client.force_login(self.admin)

        response = self.client.get(reverse('home'))

        inactive_url = reverse(
            'resend_setup_email',
            args=[self.inactive_pending.pk]
        )

        active_url = reverse(
            'resend_setup_email',
            args=[self.pending.pk]
        )

        self.assertContains(response, active_url)
        self.assertNotContains(response, inactive_url)
```

### Code walkthrough
1. `get_admin_summary()` counts active accounts, splits the active set by Admin/Manager/Sales roles, counts deactivated accounts separately, and counts active staff members whose password begins with Django's unusable-password prefix. These values are computed from the database and are not stored.
2. `get_pending_password_setup()` shows active Manager and Sales employees with unusable passwords, ordered by join date and primary key. The unbounded table is deliberate in this implementation; list size can be reconsidered during the future UI refinement if needed.
3. `get_recent_users()` returns only the five newest accounts by date joined and primary key, including inactive accounts.
4. `home(request)` passes these three results only for `role == 'admin'`. The Manager branch remains for business-level dashboard information, and the Sales branch stays restricted to personal sales and limited stock alerts.
5. The template's `Resend Setup Email` button submits a **POST** form with `{% csrf_token %}` to the existing `resend_setup_email` route. The existing view enforces Admin-only access and rejects invalid targets; there is no new email implementation.

### How we checked it
The committed source was verified on `main` at **`ccc99f3`**. The new test module contains **13 discovered test methods**, covering account counts, active-only pending users, exclusion of Admin accounts, password completion, last-five ordering, screen visibility, secure POST-only resend and role isolation. The user supplied a successful commit/push log for `ccc99f3`. The prior guarded PowerShell workflow runs Chapter 7 tests, related regression tests, `manage.py check`, a migration check and `git diff --cached --check` before committing; **the individual test outputs were not supplied in this message**, so this chapter does not claim a separately observed test count or duration. The last full-suite confirmation is **336 tests** after Chapter 5; with 12 tests added in Chapter 6 and 13 in Chapter 7, the **expected** full-suite count is **361**, pending the next full run.

### Next
Previous: [Step 377](06-sales-staff-dashboard.md#step-377). Next: Module 6 Chapter 8 — create reproducible synthetic historical sales and inventory data via a local management command.

## Chapter summary
All three role-based dashboards now have their planned functional sections. Administrator sees account status, pending password setup and the last five users without access to the Manager's business data. No new database schema was needed.

**Recall question:** Why does the pending-setup query require both an unusable password and an active Manager/Sales role?

**Source:** [Chapter 7 source commit `ccc99f3`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/ccc99f3).
