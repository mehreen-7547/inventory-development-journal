# Module 6 — Chapter 10: Final Authorization Verification and Module Completion

## Before this chapter

Module 6 Chapters 1–9 introduced the Manager, Sales Staff and Administrator dashboards and a local, synthetic 365-day inventory and sales dataset. Chapter 9 established that the generator produces consistent records and that the full **374-test** application suite passed. The final chapter checks **who may access each page** and **which actions must be submitted as POST requests**, then records the source completion marker.

## What we did

We reviewed the actual Django URL configurations and view decorators for `accounts`, `catalog`, `inventory`, `sales` and `purchasing`. We added 16 authorization tests that use temporary accounts, a test supplier, product, purchase order and sales. We made one small configuration correction: Django's built-in `@login_required` must redirect unauthenticated visitors to the application's actual `/login/` route.

No new database models, migrations, business functions or templates were needed. After the guarded final verification and source push, the user created and pushed the annotated Git tag `m6-complete`.

## Step 381 — Verify protected routes, finalize Module 6

### Before this step

Individual functional chapters tested their own pages, but a closing, cross-module check was needed. For example, Sales Staff must be able to open their own sale, not another employee's; Managers may edit stock and receive goods, but Administrators and Sales Staff may not use these stock/purchasing operations. Also, both Django's built-in `@login_required` and the project's custom `role_required` decorator must send anonymous users to a working login URL.

### What we did and why

We used Django's `TestCase`, `force_login`, `reverse`, and `subTest` to exercise real URL routing with each user role. The tests verify both permitted and rejected access, method restrictions (405 for GET requests to POST-only actions), and that unauthorized attempts do not change account, supplier, product, purchase-order or sale state.

### Code

**CLASS — `config/settings.py`** (one added setting after `AUTH_USER_MODEL = "accounts.CustomUser"`; remaining settings unchanged):

```python
AUTH_USER_MODEL = "accounts.CustomUser"

LOGIN_URL = "/login/"
```

**NEW — `core/test_module6_authorization.py`** (entire committed source from `5ce9647`):

```python
from decimal import Decimal

from django.contrib.auth import get_user_model
from django.test import TestCase
from django.urls import reverse

from catalog.models import Product, Supplier
from purchasing.models import PurchaseOrder, PurchaseOrderLine
from sales.models import Sale


class ModuleSixAuthorizationTests(TestCase):

    @classmethod
    def setUpTestData(cls):
        User = get_user_model()

        cls.admin = User.objects.create_user(
            username='final_admin',
            email='final_admin@example.com',
            password='TestPassword123!',
            role='admin'
        )

        cls.manager = User.objects.create_user(
            username='final_manager',
            email='final_manager@example.com',
            password='TestPassword123!',
            role='manager'
        )

        cls.sales = User.objects.create_user(
            username='final_sales',
            email='final_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        cls.other_sales = User.objects.create_user(
            username='final_other_sales',
            email='final_other_sales@example.com',
            password='TestPassword123!',
            role='sales'
        )

        cls.pending = User.objects.create_user(
            username='final_pending',
            email='final_pending@example.com',
            password='TestPassword123!',
            role='sales'
        )

        cls.pending.set_unusable_password()
        cls.pending.save(update_fields=['password'])

        cls.supplier = Supplier.objects.create(
            name='Final Authorization Supplier'
        )

        cls.product = Product.objects.create(
            sku='FINAL-AUTH-001',
            name='Final Authorization Product',
            category='Testing',
            supplier=cls.supplier,
            unit_price=Decimal('100.00'),
            on_hand=20,
            safety_stock=5
        )

        cls.order = PurchaseOrder.objects.create(
            po_number='FINAL-PO-001',
            supplier=cls.supplier,
            created_by=cls.manager,
            status='ordered'
        )

        PurchaseOrderLine.objects.create(
            purchase_order=cls.order,
            product=cls.product,
            quantity_ordered=10,
            unit_cost=Decimal('75.00')
        )

        cls.own_sale = Sale.objects.create(
            sale_number='FINAL-OWN',
            submission_key='final-own',
            created_by=cls.sales,
            subtotal=Decimal('100.00'),
            total=Decimal('100.00')
        )

        cls.other_sale = Sale.objects.create(
            sale_number='FINAL-OTHER',
            submission_key='final-other',
            created_by=cls.other_sales,
            subtotal=Decimal('100.00'),
            total=Decimal('100.00')
        )

    def assert_home_redirect(self, response):
        self.assertEqual(response.status_code, 302)
        self.assertEqual(
            response.url,
            reverse('home')
        )

    def test_anonymous_users_redirect_to_login(self):
        urls = [
            reverse('home'),
            reverse('employee_list'),
            reverse('supplier_list'),
            reverse('product_list'),
            reverse(
                'stock_adjustment',
                args=[self.product.pk]
            ),
            reverse('sale_list'),
            reverse(
                'sale_detail',
                args=[self.own_sale.pk]
            ),
            reverse('purchase_order_list'),
            reverse(
                'goods_receipt_create',
                args=[self.order.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    302
                )

                self.assertTrue(
                    response.url.startswith(
                        reverse('login') + '?next='
                    )
                )

    def test_dashboard_context_is_separated_by_role(self):
        cases = [
            (
                self.admin,
                'admin_summary'
            ),
            (
                self.manager,
                'manager_summary'
            ),
            (
                self.sales,
                'sales_staff_summary'
            )
        ]

        keys = [
            'admin_summary',
            'manager_summary',
            'sales_staff_summary'
        ]

        for user, expected_key in cases:
            with self.subTest(role=user.role):
                self.client.force_login(user)

                response = self.client.get(
                    reverse('home')
                )

                self.assertEqual(
                    response.status_code,
                    200
                )

                self.assertIn(
                    expected_key,
                    response.context
                )

                for key in keys:
                    if key != expected_key:
                        self.assertNotIn(
                            key,
                            response.context
                        )

    def test_admin_can_access_employee_pages(self):
        self.client.force_login(self.admin)

        urls = [
            reverse('employee_list'),
            reverse('create_employee'),
            reverse(
                'edit_employee',
                args=[self.pending.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    200
                )

    def test_other_roles_cannot_manage_employees(self):
        urls = [
            reverse('employee_list'),
            reverse('create_employee'),
            reverse(
                'edit_employee',
                args=[self.pending.pk]
            )
        ]

        actions = [
            reverse(
                'toggle_employee_status',
                args=[self.pending.pk]
            ),
            reverse(
                'resend_setup_email',
                args=[self.pending.pk]
            )
        ]

        for user in [self.manager, self.sales]:
            self.client.force_login(user)

            for url in urls:
                with self.subTest(
                    role=user.role, url=url
                ):
                    self.assert_home_redirect(
                        self.client.get(url)
                    )

            for url in actions:
                with self.subTest(
                    role=user.role, action=url
                ):
                    self.assert_home_redirect(
                        self.client.post(url)
                    )

        self.pending.refresh_from_db()
        self.assertTrue(self.pending.is_active)
        self.assertFalse(
            self.pending.has_usable_password()
        )

    def test_employee_actions_reject_get(self):
        self.client.force_login(self.admin)

        urls = [
            reverse(
                'toggle_employee_status',
                args=[self.pending.pk]
            ),
            reverse(
                'resend_setup_email',
                args=[self.pending.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    405
                )

        self.pending.refresh_from_db()
        self.assertTrue(self.pending.is_active)

    def test_manager_can_access_inventory_pages(self):
        self.client.force_login(self.manager)

        urls = [
            reverse('supplier_list'),
            reverse('supplier_create'),
            reverse(
                'supplier_edit',
                args=[self.supplier.pk]
            ),
            reverse('product_list'),
            reverse('product_create'),
            reverse(
                'product_edit',
                args=[self.product.pk]
            ),
            reverse(
                'product_stock_history',
                args=[self.product.pk]
            ),
            reverse(
                'stock_adjustment',
                args=[self.product.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    200
                )

    def test_non_managers_cannot_change_inventory(self):
        restricted_urls = [
            reverse('supplier_list'),
            reverse('supplier_create'),
            reverse(
                'supplier_edit',
                args=[self.supplier.pk]
            ),
            reverse('product_create'),
            reverse(
                'product_edit',
                args=[self.product.pk]
            ),
            reverse(
                'product_stock_history',
                args=[self.product.pk]
            ),
            reverse(
                'stock_adjustment',
                args=[self.product.pk]
            )
        ]

        status_urls = [
            reverse(
                'toggle_supplier_status',
                args=[self.supplier.pk]
            ),
            reverse(
                'toggle_product_status',
                args=[self.product.pk]
            )
        ]

        for user in [self.admin, self.sales]:
            self.client.force_login(user)

            for url in restricted_urls:
                with self.subTest(
                    role=user.role, url=url
                ):
                    self.assert_home_redirect(
                        self.client.get(url)
                    )

            for url in status_urls:
                with self.subTest(
                    role=user.role, action=url
                ):
                    self.assert_home_redirect(
                        self.client.post(url)
                    )

        self.supplier.refresh_from_db()
        self.product.refresh_from_db()

        self.assertTrue(self.supplier.is_active)
        self.assertTrue(self.product.is_active)

    def test_catalog_status_actions_reject_get(self):
        self.client.force_login(self.manager)

        urls = [
            reverse(
                'toggle_supplier_status',
                args=[self.supplier.pk]
            ),
            reverse(
                'toggle_product_status',
                args=[self.product.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    405
                )

        self.supplier.refresh_from_db()
        self.product.refresh_from_db()

        self.assertTrue(self.supplier.is_active)
        self.assertTrue(self.product.is_active)

    def test_sales_can_view_products_but_admin_cannot(self):
        self.client.force_login(self.sales)

        response = self.client.get(
            reverse('product_list')
        )

        self.assertEqual(response.status_code, 200)

        self.client.force_login(self.admin)

        self.assert_home_redirect(
            self.client.get(
                reverse('product_list')
            )
        )

    def test_manager_can_access_purchasing_pages(self):
        self.client.force_login(self.manager)

        urls = [
            reverse('purchase_order_create'),
            reverse('purchase_order_list'),
            reverse(
                'purchase_order_detail',
                args=[self.order.pk]
            ),
            reverse(
                'goods_receipt_create',
                args=[self.order.pk]
            )
        ]

        for url in urls:
            with self.subTest(url=url):
                response = self.client.get(url)

                self.assertEqual(
                    response.status_code,
                    200
                )

    def test_other_roles_cannot_access_purchasing(self):
        urls = [
            reverse('purchase_order_create'),
            reverse('purchase_order_list'),
            reverse(
                'purchase_order_detail',
                args=[self.order.pk]
            ),
            reverse(
                'goods_receipt_create',
                args=[self.order.pk]
            )
        ]

        status_url = reverse(
            'purchase_order_status',
            args=[self.order.pk]
        )

        for user in [self.admin, self.sales]:
            self.client.force_login(user)

            for url in urls:
                with self.subTest(
                    role=user.role, url=url
                ):
                    self.assert_home_redirect(
                        self.client.get(url)
                    )

            self.assert_home_redirect(
                self.client.post(
                    status_url,
                    {'status': 'cancelled'}
                )
            )

        self.order.refresh_from_db()

        self.assertEqual(
            self.order.status,
            'ordered'
        )

    def test_purchase_order_status_rejects_get(self):
        self.client.force_login(self.manager)

        response = self.client.get(
            reverse(
                'purchase_order_status',
                args=[self.order.pk]
            )
        )

        self.assertEqual(response.status_code, 405)

        self.order.refresh_from_db()
        self.assertEqual(
            self.order.status,
            'ordered'
        )

    def test_sales_pages_allow_sales_and_manager(self):
        urls = [
            reverse('sale_list'),
            reverse('sale_create'),
            reverse(
                'sale_detail',
                args=[self.own_sale.pk]
            )
        ]

        for user in [self.sales, self.manager]:
            self.client.force_login(user)

            for url in urls:
                with self.subTest(
                    role=user.role, url=url
                ):
                    response = self.client.get(url)

                    self.assertEqual(
                        response.status_code,
                        200
                    )

        self.client.force_login(self.admin)

        for url in urls:
            with self.subTest(url=url):
                self.assert_home_redirect(
                    self.client.get(url)
                )

    def test_sales_staff_cannot_view_coworker_sale(self):
        self.client.force_login(self.sales)

        own_response = self.client.get(
            reverse(
                'sale_detail',
                args=[self.own_sale.pk]
            )
        )

        self.assertEqual(
            own_response.status_code,
            200
        )

        other_response = self.client.get(
            reverse(
                'sale_detail',
                args=[self.other_sale.pk]
            )
        )

        self.assert_home_redirect(other_response)

        self.client.force_login(self.manager)

        manager_response = self.client.get(
            reverse(
                'sale_detail',
                args=[self.other_sale.pk]
            )
        )

        self.assertEqual(
            manager_response.status_code,
            200
        )

    def test_only_manager_can_open_sale_void_page(self):
        url = reverse(
            'sale_void',
            args=[self.own_sale.pk]
        )

        self.client.force_login(self.manager)

        self.assertEqual(
            self.client.get(url).status_code,
            200
        )

        for user in [self.admin, self.sales]:
            self.client.force_login(user)

            self.assert_home_redirect(
                self.client.get(url)
            )

            self.assert_home_redirect(
                self.client.post(
                    url,
                    {'reason': 'Unauthorized attempt'}
                )
            )

        self.own_sale.refresh_from_db()

        self.assertEqual(
            self.own_sale.status,
            'completed'
        )

    def test_logout_requires_post(self):
        self.client.force_login(self.admin)

        response = self.client.get(
            reverse('logout')
        )

        self.assertEqual(response.status_code, 405)

        self.assertIn(
            '_auth_user_id',
            self.client.session
        )
```

### Code walkthrough

1. **Shared test records:** `setUpTestData()` creates roles `admin`, `manager`, two Sales Staff accounts, and a pending-password-setup employee. It creates one supplier, product, purchase order/line and two sales owned by different staff. These belong only to the temporary test database.
2. **Login behavior:** `test_anonymous_users_redirect_to_login` visits nine protected URLs and checks the login redirect includes `?next=`. The added `LOGIN_URL` setting ensures the built-in Django decorator uses the same actual login route as the application's custom decorator.
3. **Dashboard role isolation:** `test_dashboard_context_is_separated_by_role` checks that exactly the appropriate `admin_summary`, `manager_summary`, or `sales_staff_summary` context is present for each role.
4. **Admin employee operations:** Only Admin can view employee list, create and edit pages or invoke activation and setup-email actions. The latter two are POST-only, and unauthorized callers must not change the pending account's state.
5. **Manager inventory operations:** Supplier and product management, stock movement history and stock adjustment are Manager-only. Sales Staff can view the product list, but not use inventory edits or status toggles. Status-changing actions reject GET.
6. **Manager purchasing operations:** Purchase-order create, list, detail and goods receipt pages are Manager-only. Other roles cannot change purchase-order status; GET is rejected on its POST-only status route.
7. **Sales privacy:** Manager and Sales Staff can access sales pages, Admin cannot. A Sales Staff employee can view their own sale detail but is redirected when requesting another employee's sale. Only Manager can open the sale-void page, and rejected attempts do not void a sale.
8. **Logout protection:** GET to the logout route is rejected with HTTP 405 and keeps the signed-in session intact.
9. **Why one helper:** `assert_home_redirect` checks both status 302 and destination `home`, avoiding duplicated assertions throughout the unauthorized-access cases.

### How we checked it

The guarded Chapter 10 PowerShell block ran the 16 new tests, verified **390 tests discovered**, ran the complete application suite, checked the populated local stock ledger, performed Django system checks, checked for pending migrations, and performed safe Git staging before committing. The user's shared final output explicitly shows:

- `Stock ledger check passed`.
- `System check identified no issues (0 silenced)`.
- `No changes detected` from the migration check.
- Git commit **`5ce9647`** with only `config/settings.py` and `core/test_module6_authorization.py` staged.
- Commit **`5ce9647`** pushed from local `main` to `origin/main`.
- Annotated tag **`m6-complete`** pushed to `origin` and shown pointing at `5ce9647`.

The user provided the final part of the terminal output rather than the earlier 16-test and 390-test `OK` lines. Since the provided block guards each step and cancels the commit/push if a test fails, its successful completion supports that the checks passed; **the exact new/full-suite test timings were not independently supplied**. The directly observed full test log from Chapter 9 remains 374/374 passed (827.910 seconds).

The CRLF/LF warning about `config/settings.py` was an informational line-ending conversion warning, not a whitespace check failure.

### Final Module 6 status

- **Manager dashboard:** Six summary cards, stock and PO attention, recent movements and sales, top sellers, seven-day sales trend.
- **Sales Staff dashboard:** Personal day/week totals, recent own sales and read-only stock alerts without purchasing/supplier details.
- **Administrator dashboard:** Account counts, role breakdown, inactive users, pending password setup and recent accounts; existing resend email action reused.
- **Synthetic historical data:** A local 365-day generator through existing sale, stock and purchasing services; 25 products/three suppliers, six high-sales days, and a verified ledger.
- **Authorization:** Role-specific route visibility, sales privacy and method restrictions covered by the closing test module.
- **Git checkpoint:** [Source commit `5ce9647`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5ce9647), tagged [`m6-complete`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/tree/m6-complete) (annotated Git tag, not a GitHub Release).
- **Local data:** Existing eight user accounts and already populated historical demo records remain intact; **do not reset or rerun** `seed_demo_data`.

### What comes next

Previous: [Step 380](09-seed-verification-and-full-regression.md#step-380).

Module 6 is closed. Before a final academic demonstration, the previously deferred work includes a responsive, role-specific navigation sidebar, consistent form/page styling (especially the goods receipt form), and read-only goods receipt history/detail linked to orders. These are **post-Module-6 review items**, not additional Module 6 requirements. Preserve the existing source checkpoint while planning these changes.

## Chapter summary

Module 6 ended with 16 new cross-role authorization tests, a corrected `LOGIN_URL`, a passed ledger check, no pending migrations, a successful source commit and the pushed `m6-complete` tag.

**Recall question:** Why is a `302` redirect from an unauthorized user not enough on its own—why do the tests also check the redirect destination and the unchanged record state?
