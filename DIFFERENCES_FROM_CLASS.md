# Differences from Class

## Basis and interpretation

Class evidence comes from the supplied DJANGO.zip, particularly backend/secondproject and backend/thirdproject. Project evidence comes from the current source main (0adf849), its README and development journal. Selected source files were inspected directly. CLASS means demonstrated in the supplied archive; EXTENDED means familiar concepts applied to a more complex workflow; NEW means a project-specific technique not evidenced in the inspected classroom examples. This is not proof that a topic was never taught verbally.

## Module 0 — Foundations

CLASS: Django project/app structure, settings, URL configuration, models and templates appear in backend/firstproject and backend/secondproject. EXTENDED: reproducible dependency files, environment-based secrets and separate AI dependencies. Project references: README_M0.md, requirements.txt, requirements-ai.txt. Git milestones and environment recovery are development practices rather than Django features.

## Module 1 — Accounts

CLASS: backend/thirdproject/doctors/models.py already defines CustomUser(AbstractUser) with a role field; doctors/views.py has role-specific dashboards. backend/secondproject/library/views.py uses authenticate, login, logout and login_required. user/views.py sends a welcome email using send_mail.

EXTENDED: project accounts/models.py defines admin, manager and sales roles and unique email. accounts/decorators.py adds reusable role_required(*roles); employee management, setup/reset links and access checks combine familiar authentication with a controlled employee lifecycle. Password setup is not the same as the class welcome email.

## Module 2 — Catalog

CLASS: backend/secondproject/products/models.py defines Product and Category with a ForeignKey; products/forms.py defines ProductForm; products/views.py uses form.is_valid(), form.save(), objects.get(), filter(), and delete(). mybooks and library demonstrate related records too.

EXTENDED: catalog/models.py adds SKU uniqueness, supplier relationship, DecimalField pricing, lead time, safety stock, non-editable on_hand and is_active. Supplier deletion is protected with on_delete=models.PROTECT rather than the class product category CASCADE example. Deactivation preserves business history; the class todo example already changes a completed flag, so Boolean state itself is not new.

## Module 3 — Stock adjustments

NEW: inventory/services.py centralizes stock changes in change_stock and adjust_stock, creates StockMovement records and uses transaction.atomic(). Unlike ordinary CRUD, a stock balance and its audit movement must be updated together. The transaction rolls back on an escaping exception. Do not claim SQLite supports row locking like PostgreSQL.

## Module 4 — Sales

EXTENDED: sales/views.py retains familiar function-based views, forms, render, redirect and messages. It adds Q-based manager search, staff ownership filtering, pagination and explicit role checks. Class products/views.py already demonstrates filtering and CRUD.

NEW: sales/services.py separates multi-line sale recording and voiding from HTTP views, uses transaction.atomic(), validates submission keys and restores stock through reversal. A void is not deletion. Dynamic line forms are an application of classroom HTML/JavaScript to multi-item transactions.

## Module 5 — Purchasing

NEW: purchasing/services.py implements create_purchase_order, change_purchase_order_status and receive_goods. Order creation and receipt are separate operations. Receipt tracks usable and damaged quantities and partial delivery; closing short ends an outstanding order without inventing stock. Transaction boundaries and submission keys protect multi-record operations. select_for_update appears in service code, but SQLite does not provide the same row-level locking semantics as PostgreSQL.

## Module 6 — Dashboards

CLASS: backend/thirdproject/doctors/views.py has role dashboards; backend/secondproject/products/views.py includes get_context_data in a class-based view. EXTENDED: role dashboards now summarize business stock, sales and purchase order information. Synthetic demo data supports repeatable workflow testing. It is not evidence of real business demand.

## Module 7 — Demand intelligence

NEW: historical daily sales, CSV import, consecutive-day validation, local pretrained Chronos-Bolt Tiny inference, quantile predictions and historical accuracy evaluation. intelligence/forecasting.py loads amazon/chronos-bolt-tiny on CPU using a cached model loader, constructs a float32 tensor, uses torch.inference_mode(), requests 30 days at quantiles 0.1, 0.5 and 0.9, checks shape and finite values, and returns dated nonnegative predictions.

NEW domain logic: stock-shortage projection, reorder calculation and rolling-average unusual-sales detection. Only the forecast is model inference; inventory rules and statistical alerts are not all AI. No model training or paid forecasting API is required.

## Module 8 — Evaluation and attention

NEW: intelligence/weekly_evaluation.py evaluates eight weeks using a 60-day history and seven actual days. It forecasts at the earlier cutoff, validates dates and values, compares AI median totals with previous-week totals and zero, and counts skipped product-weeks. The manager attention panel prioritizes existing business signals. The latest demo baseline beat AI; the evaluation is not designed to guarantee an AI win.

## Cross-cutting UI and testing

CLASS: frontend/boot.html, grid.html, media.html and backend/secondproject/templates/base2.html show existing frontend practice. EXTENDED: shared CSS, responsive role sidebar, compact forms, error placement and mobile navigation in templates/base.html and core/static/core/ui.css.

CLASS archive includes generated TestCase imports (e.g. backend/secondproject/products/tests.py). EXTENDED: substantive regression tests for permissions, stock consistency, workflows and forecasting. The latest full run passed 528/530; both remaining tests passed after a wording correction. A clean full post-fix run is not recorded.

## Classroom topics not required by this project

The archive also contains DRF serializers and API views (backend/secondproject/api and empapi), generic class-based views (products/views.py), and Celery examples (products/tasks.py and secondproject/celery.py). These are classroom concepts, not evidence that the inventory project needs REST or background workers. The implemented project uses server-rendered Django pages and local inference.

## How to explain the difference

The project builds on the same Django models, forms, views, URLs and templates learned in class. Its main extensions are business workflow integrity, role and ownership enforcement, transaction services, historical data preparation, pretrained forecasting and evaluation. The comparison is selective, not an exhaustive line-by-line audit of every archive file or source commit.
