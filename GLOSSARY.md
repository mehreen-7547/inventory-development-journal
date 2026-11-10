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

**Sale:** One completed or voided transaction record. Its model stores totals and transaction metadata; recording is implemented in `sales/services.py`; user-facing checkout pages are pending.

**SaleLine:** One product row belonging to a sale, with quantity, historical product name and selling price.

**Snapshot:** A copy of a product name or price saved at transaction time so old sales are not rewritten when catalog data changes.

**Submission key:** A unique value intended to help prevent saving the same sale twice; the service checks it and the database enforces uniqueness.

**Idempotency:** A rule that repeating the same request does not create a second transaction.

**Void:** Marking a sale as cancelled while preserving its record; reversal behavior is planned, not yet implemented.

**Choices:** The allowed labels for a model field, such as `completed` or `voided`.

**Migration history:** Django's record of database migrations already applied. Resetting it is dangerous when real records exist.

**record_sale():** The Sales service that validates a purchase, saves its lines and deducts stock in one database transaction.

**transaction.atomic():** A Django database block that either saves all its changes or rolls them back together after an error.

**IntegrityError:** A database exception, such as trying to insert a duplicate value into a unique field.

**Savepoint:** A smaller rollback boundary inside a transaction, used when handling an expected database error.

**Rollback:** Undoing all changes in a failed transaction so stock and sales remain consistent.

**patch():** A testing helper that temporarily replaces a function to simulate a failure.
