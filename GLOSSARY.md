# Common words used in the journal



Every term below is written for someone revising Django from the beginning.



**Django:** The Python framework used to build our website. It handles common tasks such as pages, users and database access.

**Python:** The programming language in which Django and our application logic are written.

**App:** A group of related Django files. We put employee-login features in an app called `accounts`.

**Project:** The whole Django website and its main settings. Ours contains several apps.

**Browser:** The program someone uses to open the website, such as Chrome.

**URL:** The address of a page, such as `/accounts/login/`. Django checks URL rules to find the right code.

**View:** A Python function or class that receives the browser's request and decides what to return.

**Template:** The HTML file that decides how a page looks and what words or fields are shown.

**Model:** A Python description of information to store, such as an employee's username, email and role.

**Database:** Where the app stores records so they remain after the website restarts.

**Migration:** A saved instruction to create or change database tables. `makemigrations` writes the instruction; `migrate` follows it.

**AbstractUser:** Django's ready-made starting point for a user record. We add an employee role to it.

**CustomUser:** The user record used by our project, based on `AbstractUser`.

**AUTH_USER_MODEL:** A setting that tells Django to use our `CustomUser` for login and accounts.

**Form:** The fields someone fills in, such as username and password. Django checks their values on submission.

**GET request:** A browser asking to view a page, usually before someone submits a form.

**POST request:** A browser sending form information or asking the server to perform an action.

**Authentication:** Checking who is signing in, usually using a username and password.

**Permission / role:** Checking what the signed-in person is allowed to see or change.

**Decorator:** A piece of Python code placed in front of a view, such as a guard that checks permission before opening a page.

**Session:** How Django remembers that a visitor is already signed in while moving between pages.

**CSRF token:** A hidden safety value Django checks when an HTML form changes something.

**is_active:** A yes/no field that can stop a user from signing in without deleting their account record.

**Django Admin:** Django's ready-made back-office site. Our own employee pages are separate from it.

**Console email:** An email printed in the VS Code terminal for testing, not delivered to a real inbox.

**SMTP:** A method programs use to ask an email provider, such as Gmail, to send a message.

**.env:** A private text file with settings for this computer, such as which Gmail account sends mail. It should not be uploaded.

**python-dotenv:** A package that lets Python read settings from a `.env` file.

**Git:** A tool that keeps a history of changes to files.

**Commit:** A saved point in Git history on your computer.

**Push:** Send local Git commits to a connected repository on GitHub.

**Branch:** A separate line of changes in one Git repository, so we can work without changing `main`.

**Virtual environment:** A private Python-and-packages folder for one project, usually named `.venv`.

**Primary key (PK):** A database identifier for a specific record. Django's `form.instance.pk` tells us whether the form is editing a saved supplier.

**ModelForm instance:** The model object attached to a form. Supplying `instance=supplier` means saving updates that supplier rather than inserting a new row.

**Query string:** Extra URL data after `?`, such as `?mode=edit`. We use it for list selection mode, not access control.

**Static file:** A CSS, image or JavaScript asset served separately from the HTML template. Catalog's stylesheet lives at `catalog/static/catalog/style.css`.

**findstatic:** A Django management command that shows whether Django can discover a named static asset and where it looked.

**Soft deactivation:** Marking a record inactive instead of deleting it. A supplier remains in the database and can be reactivated.

**require_POST:** Django decorator that accepts only POST requests for a view and rejects GET with HTTP 405.

**HTTP 405:** “Method Not Allowed.” A GET request cannot trigger this supplier status change.

**refresh_from_db():** Django model method that reloads saved values from the database, useful after a test request.

**TestCase / setUp():** Django's automated test class and per-test preparation method for repeatable tests.

**assertRedirects():** Django test assertion checking a redirect goes to the expected page.

**SKU:** A unique code identifying a product, such as `TEST-001`.

**ForeignKey:** A model field linking one database record to another, such as a product to its supplier.

**PROTECT:** A Django deletion rule that prevents deleting a supplier while products still refer to it.

**DecimalField:** A database field for fixed-precision numbers, useful for prices.

**select_related():** Loads linked supplier data with products in one database query.

**editable=False:** Prevents a model field such as `on_hand` from appearing in ordinary ModelForms; it is not a complete security control by itself.

**instance=product:** Tells ModelForm to edit an existing Product rather than create another.

**distinct():** Removes duplicate database results, used when combining supplier choices.

**Stock ledger:** A history of every change to a product's stock quantity.

**Stock movement:** One recorded addition or reduction, including quantity before and after.

**Quantity delta:** The signed stock change, such as +5 for a delivery or -2 for damaged items.

**Management command:** A Django task run in the terminal using `python manage.py <command_name>`.

**BaseCommand:** Django class used as the starting point for a management command.

**CommandError:** A Django error that marks a management command as failed.

**Ledger integrity:** Checking that consecutive movement balances and the product's final stock quantity agree.

**StringIO:** A Python object used to capture text output in automated tests.

**Sale:** One completed or voided transaction record. Its model stores totals and transaction metadata; recording is implemented in `sales/services.py`; checkout pages and voiding interface are implemented.

**SaleLine:** One product row belonging to a sale, with quantity, historical product name and selling price.

**Snapshot:** A copy of a product name or price saved at transaction time so old sales are not rewritten when catalog data changes.

**Submission key:** A unique value intended to help prevent saving the same sale twice; the service checks it and the database enforces uniqueness.

**Idempotency:** A rule that repeating the same request does not create a second transaction.

**Void:** Marking a sale as cancelled while preserving its record; reversal is implemented by the manager-only void_sale() service.

**Choices:** The allowed labels for a model field, such as `completed` or `voided`.

**Migration history:** Django's record of database migrations already applied. Resetting it is dangerous when real records exist.

**record_sale():** The Sales service that validates a purchase, saves its lines and deducts stock in one database transaction.

**transaction.atomic():** A Django database block that either saves all its changes or rolls them back together after an error.

**IntegrityError:** A database exception, such as trying to insert a duplicate value into a unique field.

**Savepoint:** A smaller rollback boundary inside a transaction, used when handling an expected database error.

**Rollback:** Undoing all changes in a failed transaction so stock and sales remain consistent.

**patch():** A testing helper that temporarily replaces a function to simulate a failure.

**Form.clean():** A Django form method for checking relationships between fields, such as duplicate product selections.

**ModelChoiceField:** A form field that accepts only model records from a specified queryset.

**CSRF token:** A hidden form security value Django checks to protect POST submissions.

**Post/Redirect/Get:** Redirecting after a successful POST so refreshing the confirmation page does not repeat the submission.

**Sale detail page:** A protected view of a saved transaction and its line items.

**Paginator:** Django helper that divides a queryset into pages, here ten sales per page.

**Q object:** Django query helper for combining filters with OR or AND, such as searching a sale number or employee username.

**select_related():** Fetches a related database record in the same query, avoiding extra queries for each sale's creator.

**icontains:** A case-insensitive substring lookup used in sale-history search.

**GET filter:** Form inputs placed in the URL query string, so a history search can be bookmarked or shared (subject to access control).

**Role-scoped queryset:** A database query restricted to the records a user's role is permitted to see, before applying optional search filters.

**void_sale():** Manager-only Sales service that restores quantities from a completed sale, records void movements and saves audit metadata atomically.

**select_for_update():** Requests a database row lock until the transaction ends on databases supporting row-level locking; SQLite does not provide equivalent row-level locking.

**Void reason:** Required explanation entered by a manager before reversing a sale; stored with the transaction.

**Void audit metadata:** The user, timestamp and reason saved when a sale is voided.

**Purchase order (PO):** A record of goods the business intends to buy from a supplier; creating it does not increase physical stock.

**Purchase order line:** A product, quantity ordered and agreed unit cost within a purchase order.

**Draft purchase order:** An order being prepared, not yet issued to the supplier.

**Goods receipt:** A saved supplier delivery, with usable and damaged quantities, linked to a purchase order.

**TabularInline:** A Django Admin configuration for editing related records in rows on the parent record's admin page.

**create_purchase_order():** Manager-only Purchasing service that validates the supplier, products, quantities, costs and PO number, then saves the order and lines atomically without increasing stock.

**PO number uniqueness:** A unique database constraint prevents two purchase orders from sharing the same identifier, even if an earlier application-level lookup misses a duplicate.

**Trailing whitespace:** Spaces or tabs at the ends of lines; staged Git checks may reject these and extra blank lines at the end of a file.


**Submission key / idempotency key:** A unique value used to prevent one delivery from being processed twice.

**Partial receipt:** A delivery that leaves some ordered quantity still outstanding.

**Close Short:** A final status for a partly delivered order when the remaining goods will not arrive; it does not increase stock.

**Atomic transaction:** Related database changes that all succeed together or all roll back if one fails.

## Historical CSV import terms (Module 7 Chapter 2)

**CSV:** Comma-separated values; a text format with one row per record and a header naming columns.

**SKU validation:** Checking that every product code in the CSV exists in the catalog before importing.

**Decimal:** Python's precise base-10 number type, useful for money rather than binary floating-point arithmetic.

**Atomic transaction:** A database operation that commits all its writes together or rolls them all back if one fails.

**update_or_create:** Django ORM method that updates an existing matching record or creates it if absent.

**Rollback:** Undoing database writes made in a transaction when an error occurs.

**SimpleTestCase:** Django test class useful for code that does not query the database.

**TestCase:** Django test class with isolated database support for model-dependent tests.

**Mock / patch.object:** Temporarily replacing a method in a test to simulate behavior such as a database failure.

**Idempotent import:** Importing the same product/date data again without creating duplicate DailySales rows.

**Date-dependent test:** A test whose expected output changes with the calendar; avoid hardcoded totals when weekdays influence generated data.

## Historical data reconciliation (Module 7 Chapter 3)

**Source ownership:** The `DailySales.source` label describing whether a daily summary is `recorded` (from transactions) or `imported` (from CSV).

**Recorded-sales priority:** A recorded summary cannot be replaced by a later CSV import; synchronizing recorded history can replace an imported summary in the specified date range.

**Recorded zero-sales day:** A date inside a synchronized range where completed transactions for a product total zero; still marked `recorded` and protected from CSV replacement.

**Synchronization coverage assumption:** The project treats the explicitly synchronized date range as authoritative even when a product has zero sales; it does not independently prove that every historical transaction is present.

**Migration default:** A value assigned to existing rows when a new model field is added; `recorded` is assumed for old DailySales rows and does not reconstruct their origin.

**Whitespace cleanup:** Removing trailing spaces and extra final blank lines before `git diff --cached --check`, then rerunning focused tests and committing only intended files.

## Forecasting data preparation (Module 7 Chapter 4)

**Demand history:** The number of product units sold on each past day, used as input for future forecasting.

**Lookback window:** The period of past days supplied to the forecasting model; this project currently requires at least 60 consecutive days.

**Forecast horizon:** How far ahead predictions extend; the planned AI horizon is 30 days, not implemented in Chapter 4.

**Missing day versus zero sales:** A missing database record is unknown history; a saved record with zero units is a valid zero-demand observation.

**Read-only service:** A function that retrieves and transforms data without changing stored records.

**Pretrained time-series foundation model:** An AI model trained previously on time-series data; the planned Chronos-Bolt Tiny integration uses inference without local training.

**MAE (mean absolute error):** The average absolute difference between predicted and actual sales; planned for future model evaluation.

**Moving-average baseline:** A simple prediction based on recent sales averages, used to compare whether the AI forecast improves accuracy.

## Chronos-Bolt forecasting (Module 7 Chapter 5)

**Chronos-Bolt Tiny:** A pretrained transformer-based time-series foundation model used locally to forecast numerical sequences without model training.

**Inference:** Running an already trained model to obtain predictions, without updating its weights.

**Tensor:** PyTorch's numerical array type; the input shape `(1, 60)` means one sequence containing 60 daily values.

**Batch dimension:** The leading dimension added with `unsqueeze(0)` so the model receives one sequence as a batch.

**P10 / P50 / P90:** Model-predicted 10th, 50th and 90th quantiles for future demand; estimates rather than guarantees.

**Model cache (`lru_cache`):** Reuses the loaded model in the current Python process; does not store predictions in the database.

**`torch.inference_mode()`:** PyTorch context for prediction without gradient tracking.

**Mocked model test:** Replaces real model calls with controlled outputs to test service behavior quickly and deterministically.

**Feasibility probe:** Checks whether pretrained inference runs on local hardware; unlike an accuracy evaluation, it does not establish whether forecasts match future sales.

## Forecast evaluation (Module 7 Chapter 6)

**Chronological holdout:** Evaluating on later observations that were not supplied to the forecasting model at its cutoff date.

**Inference context:** Historical values passed to an already pretrained model; not local model training.

**Evaluation cutoff:** The final date the model is permitted to use as history before predicting held-out days.

**MAE (mean absolute error):** Average of absolute daily differences between actual and predicted units; lower means better on that evaluation period.

**P50 point forecast:** The model's central/median quantile, used here to calculate MAE.

**Moving-average baseline:** Simple comparison method; here, the last seven pre-cutoff days are averaged once and that same value predicts all 30 future days.

**Data leakage:** Accidentally allowing information from the test period into model inputs or baseline calculations, making accuracy look unrealistically good.

**Single-window backtest:** Testing on one historical forecast origin and future period; results may vary for other dates and products.

**Model comparison:** Scoring two forecasting methods against the same actual observations; a lower score in one test does not prove general superiority.
