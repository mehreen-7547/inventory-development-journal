# Module 7 — Chapter 2: Historical CSV Import (Steps 392–405)

## Chapter introduction

Chapter 1 created a daily per-product history table. Chapter 2 adds a second source of historical demand: CSV rows supplied by a user. The import deliberately writes **DailySales summaries**, not completed sales transactions, stock movements or on-hand inventory. This separation matters: a historical summary is not proof that a checkout occurred in this application.

**Verified source:** [commit `adb1355`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/adb1355) on `main`; compared against `21d51cf`. **Journal branch:** `study-2`. **Observed tests:** 18 focused CSV tests passed; full Django suite **421/421 passed** in 729.967 seconds. The source commit contains four files: two new and two modified.

## CSV contract

```csv
date,sku,units_sold,gross_revenue
2025-11-10,BRD-001,3,345.00
2025-11-11,BRD-001,5,575.00
2025-11-11,BRD-002,2,170.00
```

One row means the total for **one SKU on one calendar date**, not an individual Sale or SaleLine. Dates use ISO `YYYY-MM-DD`; quantities are nonnegative whole numbers; revenue is finite, nonnegative, at most two decimal places, and must be zero if quantity is zero. A repeated SKU/date pair within one CSV is rejected. Existing SKU records are required.

## Step-by-step development

### Step 392 — Validate the CSV structure and row values

**Before this step.** DailySales could already be populated from recorded sales, but it had no safe entry point for external daily history. An importer that saved raw text directly could accept malformed dates, duplicate product-days or invalid revenue. We first built a pure parser so every row could be checked *before* touching the database.

**NEW — `intelligence/csv_validation.py` (actual committed excerpts)**

```python
EXPECTED_COLUMNS = [
    'date',
    'sku',
    'units_sold',
    'gross_revenue'
]

def validate_daily_sales_csv(csv_text):
    if not isinstance(csv_text, str) or not csv_text.strip():
        raise ValueError('CSV file is empty')

    reader = csv.reader(StringIO(csv_text.lstrip('\ufeff')))
    header = next(reader, None)

    if header != EXPECTED_COLUMNS:
        raise ValueError(
            'CSV columns must be: '
            'date,sku,units_sold,gross_revenue'
        )

    validated_rows = []
    seen = set()
```

**Walkthrough.** `csv.reader` understands CSV fields, unlike splitting text on commas manually. `StringIO` lets it read a string as if it were a file. The `lstrip('\ufeff')` removes an optional UTF-8 byte-order mark from the start. The header comparison enforces the column names *and their order*. `seen` is prepared for detecting repeated SKU/date keys, and `validated_rows` collects only successfully parsed records. Validation errors raise `ValueError` with an explanation; they do not write any data.

**NEW — `intelligence/csv_validation.py` (row parsing and checks)**

```python
for row in reader:
    if not row:
        continue
    line_number = reader.line_num
    if len(row) != 4:
        raise ValueError(
            f'Line {line_number}: Expected four values'
        )

    sale_date, sku, units, revenue = [
        value.strip() for value in row
    ]
    try:
        parsed_date = datetime.strptime(
            sale_date, '%Y-%m-%d'
        ).date()
        if parsed_date.isoformat() != sale_date:
            raise ValueError()
    except ValueError:
        raise ValueError(
            f'Line {line_number}: Invalid date'
        )
```

**Walkthrough.** Blank rows are skipped, while rows with an incorrect number of fields are rejected. Whitespace around values is removed before validation. The date parser converts a string into a Python `date`, and the ISO-format round trip prevents alternate date spellings from being accepted. The line number helps locate bad input. The rest of this same committed function checks SKU length, ASCII digits for units, `Decimal` revenue, finite/nonnegative amounts, at most two decimal places, and duplicate `(sku, date)` pairs. Finally, it rejects a header-only CSV and returns dictionaries containing typed values.

**How checked.** Later covered by eight focused parser tests and the 18-test CSV suite. No standalone Step 392 terminal output is retained in this handoff.

**Previous:** [Step 391](01-daily-sales-history-foundation.md#step-391). **Next:** [Step 393](#step-393--test-csv-validation).

### Step 393 — Test valid and invalid CSV input

**Before this step.** A parser can appear correct for a happy-path sample while failing on empty files or duplicated dates. We added small, isolated tests so changes to the parser would be checked automatically. These tests do not need a database because they only exercise Python parsing.

**NEW — `intelligence/test_csv_validation.py` (actual committed test examples)**

```python
class DailySalesCSVValidationTests(SimpleTestCase):

    def test_valid_csv(self):
        csv_text = (
            'date,sku,units_sold,gross_revenue\n'
            '2025-11-10,BRD-001,3,345.00\n'
        )
        rows = validate_daily_sales_csv(csv_text)
        self.assertEqual(len(rows), 1)
        self.assertEqual(rows[0]['date'], date(2025, 11, 10))
        self.assertEqual(rows[0]['sku'], 'BRD-001')
        self.assertEqual(rows[0]['units_sold'], 3)

    def test_empty_csv(self):
        with self.assertRaises(ValueError):
            validate_daily_sales_csv('')
```

**Walkthrough.** `SimpleTestCase` is sufficient because no model queries are made. The valid example proves that the parser returns a real `date`, an SKU string and an integer quantity, rather than leaving everything as raw CSV text. `assertRaises` verifies rejection rather than checking an exact error message. The same test class also checks wrong headers, invalid dates, missing SKU, negative quantity, invalid revenue and duplicate product/date pairs. This is a behavior-focused safety net.

**How checked.** Eight parser tests were added and passed during development; all remain in the committed 18-test file.

**Next:** [Step 394](#step-394--verify-skus-against-products).

### Step 394 — Verify SKUs against actual Product records

**Before this step.** A syntactically valid SKU is not necessarily a product in this inventory database. Saving historical data for an unknown product would either fail late or misrepresent which product was sold. We added a second validation layer after parsing and before any insert.

**CLASS — `intelligence/csv_validation.py` (actual committed function)**

```python
def validate_product_skus(validated_rows):
    from catalog.models import Product

    csv_skus = {
        row['sku']
        for row in validated_rows
    }
    existing_skus = set(
        Product.objects.filter(
            sku__in=csv_skus
        ).values_list('sku', flat=True)
    )
    missing_skus = csv_skus - existing_skus
    if missing_skus:
        raise ValueError(
            'Unknown product SKU(s): '
            + ', '.join(sorted(missing_skus))
        )
    return validated_rows
```

**Walkthrough.** A set removes repeated SKUs, so the database needs to check each distinct product code only once. `sku__in` asks Django for matching products, and `values_list(..., flat=True)` returns the matching SKU values without constructing full Product objects. Set subtraction identifies codes present in the CSV but absent from the catalog. The sorted error text gives a stable, readable list of missing codes. Returning the original validated rows lets the service continue with the typed data.

**How checked.** Covered by three SKU tests in Step 395.

**Next:** [Step 395](#step-395--test-sku-validation).

### Step 395 — Test known and unknown SKUs

**Before this step.** The database-backed validator needed tests against real Product records. A fabricated SKU should be rejected before importing, and multiple missing SKUs should all be reported. We added a test fixture with a Supplier and Product to isolate the validation behavior.

**NEW — `intelligence/test_csv_validation.py` (actual committed excerpt)**

```python
class ProductSKUValidationTests(TestCase):

    def setUp(self):
        self.supplier = Supplier.objects.create(
            name='Test Supplier'
        )
        self.product = Product.objects.create(
            sku='BRD-001',
            name='Test Bread',
            category='Bakery',
            supplier=self.supplier,
            unit_price=Decimal('115.00')
        )

    def test_unknown_sku_is_rejected(self):
        rows = [{
            'date': date(2025, 11, 10),
            'sku': 'BRD-999',
            'units_sold': 3,
            'gross_revenue': Decimal('345.00')
        }]
        with self.assertRaisesRegex(
            ValueError,
            'Unknown product SKU'
        ):
            validate_product_skus(rows)
```

**Walkthrough.** `TestCase` creates isolated database state, so the test never depends on which products happen to exist in the user's local SQLite file. `setUp` supplies the minimum related records required by Product. The unknown-SKU test deliberately uses `BRD-999` rather than the created `BRD-001`; it verifies both that an error occurs and that the message explains the problem. Two companion tests accept an existing SKU and report multiple unknown codes.

**How checked.** Eleven cumulative CSV/SKU tests passed.

**Next:** [Step 396](#step-396--implement-the-atomic-import-service).

### Step 396 — Implement the atomic import service

**Before this step.** The two validators could now reject malformed CSV and missing products, but they did not persist any historical summaries. The service must use the existing DailySales table and be safe to rerun. It must never call stock-adjustment or sale-recording services for historical summary data.

**CLASS — `intelligence/services.py` (actual committed function)**

```python
def import_historical_daily_sales(csv_text):
    from .csv_validation import (
        validate_daily_sales_csv,
        validate_product_skus
    )
    validated_rows = validate_daily_sales_csv(csv_text)
    validate_product_skus(validated_rows)

    skus = {
        row['sku']
        for row in validated_rows
    }
    products = {
        product.sku: product
        for product in Product.objects.filter(
            sku__in=skus
        )
    }

    with transaction.atomic():
        for row in validated_rows:
            DailySales.objects.update_or_create(
                product=products[row['sku']],
                date=row['date'],
                defaults={
                    'units_sold': row['units_sold'],
                    'gross_revenue': row['gross_revenue']
                }
            )

    return len(validated_rows)
```

**Walkthrough.** Both validators run before the transaction starts saving rows. The product dictionary converts each SKU into its matching Product object without querying once per CSV line. `transaction.atomic()` makes all database writes one unit: if a later write fails, earlier writes in that same import are rolled back. `update_or_create` updates an existing product/date summary instead of adding duplicates. The return value is the number of validated rows processed, not a count of newly inserted rows. Importantly, this function does not touch `Product.on_hand`, `Sale`, `SaleLine` or `StockMovement`.

**How checked.** Creation, reimport, missing SKU and stock isolation tests were added next.

**Next:** [Step 397](#step-397--test-import-behavior).

### Step 397 — Test creation, reimport, missing SKU and stock isolation

**Before this step.** The service needed evidence that it saved correct daily values and did not accidentally change inventory. Reimporting an already known date should update the row rather than duplicate it. A mixed CSV containing an unknown SKU should save nothing.

**NEW — `intelligence/test_csv_validation.py` (actual committed excerpt)**

```python
class HistoricalSalesImportTests(TestCase):
    def setUp(self):
        self.supplier = Supplier.objects.create(
            name='Import Test Supplier'
        )
        self.product = Product.objects.create(
            sku='IMP-001',
            name='Import Test Product',
            category='Stationery',
            supplier=self.supplier,
            unit_price=Decimal('50.00')
        )

    def test_import_does_not_change_stock(self):
        original_stock = self.product.on_hand
        csv_text = (
            'date,sku,units_sold,gross_revenue\n'
            '2025-11-10,IMP-001,3,150.00\n'
        )
        import_historical_daily_sales(csv_text)
        self.product.refresh_from_db()
        self.assertEqual(
            self.product.on_hand,
            original_stock
        )
```

**Walkthrough.** The fixture creates an actual catalog product, making the importer pass SKU validation. The stock test saves the initial `on_hand` value, imports a historical row, reloads Product from the database, and checks that stock is unchanged. The reload is essential: otherwise the assertion might compare a stale Python object. Other tests check persisted quantity/revenue, reimport update without duplication, and rejection of an unknown SKU before saving any records.

**How checked.** Fifteen cumulative focused tests passed.

**Next:** [Step 398](#step-398--prove-rollback-on-database-failure).

### Step 398 — Prove rollback on database failure

**Before this step.** Merely writing `transaction.atomic()` is not enough to demonstrate the promised all-or-nothing behavior. We needed a failure after the first row had already been saved, so that a partially committed import would be detectable. A controlled mock produces that failure without damaging real data.

**NEW — `intelligence/test_csv_validation.py` (actual committed excerpt)**

```python
original_update = DailySales.objects.update_or_create
call_count = 0

def fail_on_second_save(*args, **kwargs):
    nonlocal call_count
    call_count += 1
    if call_count == 2:
        raise RuntimeError('Simulated database failure')
    return original_update(*args, **kwargs)

with patch.object(
    DailySales.objects,
    'update_or_create',
    side_effect=fail_on_second_save
):
    with self.assertRaises(RuntimeError):
        import_historical_daily_sales(csv_text)

self.assertEqual(DailySales.objects.count(), 0)
```

**Walkthrough.** The test stores the real `update_or_create` method and wraps it with a counter. On the first call, the real database operation proceeds; on the second, the mock raises a deliberate exception. If the transaction were not atomic, the first DailySales row could remain. The final count of zero confirms the entire import was rolled back. `patch.object` is temporary and applies only within its context manager, so other tests use the normal manager method.

**How checked.** Sixteen cumulative focused tests passed.

**Next:** [Step 399](#step-399--reject-header-only-csv).

### Step 399 — Reject header-only CSV

**Before this step.** A file containing only valid column names has no historical data. Treating it as a successful import could mislead the user into believing records were loaded. The validator already rejects an empty `validated_rows` list, so we added an integration test for this boundary.

**NEW — `intelligence/test_csv_validation.py` (actual committed test)**

```python
def test_header_only_csv_is_rejected(self):
    csv_text = (
        'date,sku,units_sold,gross_revenue\n'
    )
    with self.assertRaises(ValueError):
        import_historical_daily_sales(csv_text)
    self.assertEqual(
        DailySales.objects.count(),
        0
    )
```

**Walkthrough.** The test sends a correctly formatted header without any rows. It expects the importer to propagate `ValueError` and verifies the database remains empty. During development, the first targeted invocation failed because the test method was not correctly located in its test class; the indentation was corrected, and the isolated test passed. This was a test-placement error, not a defect in the import service. The committed file contains the method inside `HistoricalSalesImportTests`.

**How checked.** Isolated test passed after correction.

**Next:** [Step 400](#step-400--import-more-than-one-product).

### Step 400 — Import more than one product

**Before this step.** A single-product CSV could pass even if the importer incorrectly reused the first Product for every row. We added a second Product and two CSV rows on the same date to verify that each SKU resolves independently. This reflects real inventory history with multiple products sold per day.

**NEW — `intelligence/test_csv_validation.py` (actual committed excerpt)**

```python
csv_text = (
    'date,sku,units_sold,gross_revenue\n'
    '2025-11-10,IMP-001,3,150.00\n'
    '2025-11-10,IMP-002,4,100.00\n'
)
count = import_historical_daily_sales(csv_text)
self.assertEqual(count, 2)
self.assertEqual(DailySales.objects.count(), 2)
```

**Walkthrough.** The complete committed test first creates `IMP-002` alongside the existing `IMP-001`. Both records share a date but not a product, so they are distinct daily summaries. The assertions check that two CSV rows are processed and two DailySales rows exist. Additional assertions retrieve the rows by Product and verify their separate quantities and revenues. This catches incorrect SKU mapping while preserving the intended one-row-per-product-per-date rule.

**How checked.** Isolated multiple-product test passed.

**Next:** [Step 401](#step-401--rerun-the-focused-csv-tests).

### Step 401 — Rerun the focused CSV tests

**Before this step.** Tests were added incrementally across parser, SKU lookup and database import. A final focused run confirms the test methods are discoverable together and do not interfere with one another. It also establishes a compact regression command for future edits to this feature.

**CLASS — command (not a source file)**

```powershell
python manage.py test intelligence.test_csv_validation
```

**Walkthrough.** Django imports the named test module, discovers `SimpleTestCase` and `TestCase` methods, and creates a temporary test database for the database-backed cases. The parser cases do not require database queries; SKU and import cases do. The combined count matters because a misplaced method could silently disappear from discovery. The user reported **18 tests passed**, covering eight parser checks, three SKU checks and seven import checks.

**How checked.** 18/18 passed.

**Next:** [Step 402](#step-402--run-the-full-suite-and-investigate-a-regression).

### Step 402 — Run the full suite and investigate a regression

**Before this step.** Focused tests cannot catch failures elsewhere in the Django project. The full regression suite also includes older demo-data tests. The first full run exposed one pre-existing brittle expectation about a date-dependent sales count.

**CLASS — `inventory/test_seed_integration.py` (original failing assertion)**

```python
self.assertEqual(Sale.objects.count(), 1085)
```

**Walkthrough.** The demo seed uses `timezone.localdate() - timedelta(days=365)` to select its first day. It generates different daily counts for weekdays, weekends and anomaly days. Because the starting weekday changes as the calendar advances, an exact count of 1,085 is not a stable invariant. The observed result was 1,080, not 1,085. The failure was in the old integration test rather than in the new CSV importer. The full run reported **420 of 421 tests passed**, with this one failure.

**How checked.** Full suite: 421 discovered, one failure, 1,227.906 seconds.

**Next:** [Step 402A](#step-402a--isolate-the-failing-test).

### Step 402A — Isolate the failing test

**Before this step.** A full-suite failure can arise from test interference, shared fixtures or a genuine deterministic bug. We ran the failing method by itself to distinguish those possibilities. The isolated test reproduced the same mismatch, pointing to its own date-sensitive expectation.

**CLASS — command (not a source file)**

```powershell
python manage.py test inventory.test_seed_integration.SeedDemoDataIntegrationTests.test_sales_cover_all_365_days
```

**Walkthrough.** The dotted test path targets a single method within the named class and module. Django still creates an isolated test database and runs that class's seed setup, but it skips the other 420 tests. The result again showed `1080 != 1085`, proving the problem did not require the rest of the test suite. The isolated run took 6.564 seconds. No code was changed during isolation.

**How checked.** One isolated test failed with the same assertion.

**Next:** [Step 402B](#step-402b--repair-the-date-sensitive-test).

### Step 402B — Repair the date-sensitive test

**Before this step.** The generator was behaving consistently with its rules; the hardcoded assertion was the problem. Replacing 1,085 with 1,080 would only make the test pass temporarily. We changed the test to verify a defensible lower bound and that every generated sale was completed, retaining its separate 365-day coverage assertions.

**CLASS — `inventory/test_seed_integration.py` (actual committed correction)**

```python
def test_sales_cover_all_365_days(self):
    total_sales = Sale.objects.count()

    self.assertGreaterEqual(total_sales, 730)

    self.assertEqual(
        Sale.objects.filter(
            status='completed'
        ).count(),
        total_sales
    )
```

**Walkthrough.** The lower bound 730 is two sales per day across 365 days; the seed's ordinary weekdays have at least two, weekends at least three and anomaly days at least twelve. The equality check ensures none of the seeded Sale rows has an unexpected status. The rest of the original method still checks the exact list of 365 dates, that SaleLine rows exist beyond the number of Sales, and that no sale is dated today or later. This preserves useful coverage without assuming an unchanging calendar composition.

**How checked.** Isolated test passed: 1/1 in 6.550 seconds.

**Next:** [Step 403](#step-403--rerun-the-full-regression-suite).

### Step 403 — Rerun the full regression suite

**Before this step.** Fixing one test must not conceal new failures elsewhere. We reran the complete Django test suite after the integration-test correction. This is the chapter's definitive local regression milestone, covering the new importer and existing application features.

**CLASS — command (not a source file)**

```powershell
python manage.py test
```

**Walkthrough.** Running `manage.py test` without an app label discovers tests across all installed Django apps. It creates a separate test database and destroys it afterward, rather than modifying the normal local database. The user supplied terminal output showing 421 discovered tests, zero system-check issues, and `OK` after 729.967 seconds. This verifies that the date-sensitive correction and the historical CSV importer coexist with the previously implemented modules.

**How checked.** **421/421 passed**; 729.967 seconds.

**Next:** [Step 404](#step-404--review-changed-files).

### Step 404 — Review changed files

**Before this step.** A successful test run does not tell Git which files belong to a chapter. Two unrelated untracked local files were present and must not be included in the import commit. We inspected status and the diff before explicitly staging the four relevant source files.

**CLASS — commands (not a source file)**

```powershell
git status --short
git diff --stat
```

**Walkthrough.** Git showed modified `intelligence/services.py` and `inventory/test_seed_integration.py`, plus new `intelligence/csv_validation.py` and `intelligence/test_csv_validation.py`. It also showed `purchasing.zip` and `reset_business_data_once.py` as untracked. The latter two were not part of this chapter, so the commit uses explicit paths instead of `git add .`. The `git diff --stat` command only summarizes tracked changes; it does not include new untracked files until staged.

**How checked.** Four chapter files identified; two unrelated local files excluded.

**Next:** [Step 405](#step-405--commit-and-push-the-source).

### Step 405 — Commit and push the source

**Before this step.** The complete suite passed and the chapter's four files were identified. We staged only those paths, checked the staged diff, committed and pushed to the source repository's `main` branch. The journal must be written from the actual pushed files, not assumed local edits.

**CLASS — commands (not a source file)**

```powershell
git add intelligence/services.py intelligence/csv_validation.py intelligence/test_csv_validation.py inventory/test_seed_integration.py
git diff --cached --check
git diff --cached --stat
git commit -m "Add validated historical daily sales CSV import"
git push origin main
git status --short
```

**Walkthrough.** `git add` explicitly stages the four intended paths. `git diff --cached --check` reported trailing whitespace and extra end-of-file blank lines; these were **warnings, not corrected before the commit**. The commit nevertheless succeeded as `adb1355`, and GitHub accepted the push from `21d51cf` to `adb1355`. Final status still listed only the two unrelated untracked files. The warnings are cosmetic cleanup for later and must not be misrepresented as a clean whitespace check.

**How checked.** Commit `adb1355` pushed successfully; GitHub comparison independently verified exactly four changed source files (139 + 40 + 336 + 6 changed lines).

**Next:** [Module 7 Chapter 3](#chapter-summary-and-next-work).

## How the four committed files connect

1. **`intelligence/csv_validation.py`** parses CSV text and validates the shape, values and existing product SKUs. It raises errors before persistence.
2. **`intelligence/services.py`** calls both validators, maps SKUs to Products and upserts `DailySales` records inside one transaction.
3. **`intelligence/test_csv_validation.py`** verifies the parser, SKU checks, import, idempotent reimport, stock isolation, multiple products, empty data and rollback.
4. **`inventory/test_seed_integration.py`** corrects an unrelated historical demo-seed assertion revealed by the full regression run.

**Important limitation:** No upload form, management command or automatic forecasting job is introduced by this commit. The importer is a Python service, not yet an end-user CSV upload interface. It does not create Sale or SaleLine transactions. Existing DailySales rows for a matching product/date can be overwritten by import; deciding how to reconcile imported history with re-synchronization from recorded transactions belongs to later workflow design.

## Chapter summary and next work

The application can now validate historical daily sales in a strict CSV format, reject unknown products, and atomically insert/update DailySales summaries without changing stock. The 18 focused tests and 421-test full regression suite passed. Source commit `adb1355` is pushed to `main`; the frozen Core tag `m6-complete` is unchanged. The next chapter should first define how imported summaries coexist with generated daily summaries before exposing an upload UI or using the data for forecasting.

**Previous chapter:** [Module 7 Chapter 1](01-daily-sales-history-foundation.md). **Next:** Module 7 Chapter 3 (scope and first step to be confirmed).

**Recall question:** Why is it safer to validate every CSV row and product SKU before using `transaction.atomic()` to write DailySales records?
