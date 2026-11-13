# Module 7 — Chapter 3: Historical Data Reconciliation (Steps 407–419)

## Chapter introduction

The application has two sources of daily sales summaries: completed Sales/SaleLine transactions and historical CSV imports. Both previously wrote the same product/date in `DailySales`, meaning the last writer could overwrite the more authoritative total. This chapter implements the approved rule: **recorded transactions win within the synchronized date range; CSV can supply historical data outside it**. A recorded zero-sales day is still recorded history.

**Verified source:** [commit `ff0c68d`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/ff0c68d) on `main`; GitHub comparison against `adb1355` shows five changed files (172 insertions, 7 deletions). **Journal branch:** `study-2`.

**Source files:** `intelligence/models.py` (MODIFIED), `intelligence/migrations/0002_dailysales_source.py` (NEW), `intelligence/services.py` (MODIFIED), `intelligence/test_csv_validation.py` (MODIFIED), `intelligence/test_daily_sales.py` (MODIFIED).

**Observed checks:** 29 focused tests passed in 9.138s; full suite 427/427 passed in 617.512s; after whitespace cleanup 29 focused tests passed in 4.473s. No system-check issues; no pending model changes. The user's local Git push was successful.

## Core business rule and limitation

A `DailySales` row now has `source='recorded'` or `source='imported'`. `save_product_daily_sales(product_id,start_date,end_date)` calculates every day in its supplied range from completed sales and writes `source='recorded'`, even for zero-unit days. `save_all_product_daily_sales()` calls this from the earliest completed Sale date to the local current date. `import_historical_daily_sales(csv_text)` skips any existing recorded row, but creates or updates imported rows. This is **range-based authority**, not a separately verified transaction-coverage calendar; it assumes the synchronization range is sufficiently complete. An imported row inside the range may temporarily exist until the next sync. The import return count reports validated rows, not rows actually written.

## Step-by-step journal

### Step 407 — Approve priority rule

**Before this step.** Before this step, both CSV import and transaction synchronization used update_or_create for the same product/date, so whichever ran last replaced the other. We selected transaction-derived summaries as authoritative within an explicitly synchronized period, while CSV supplies history not covered by that period.

**What we did.** No source edit; design decision.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** A zero-unit summary can be a genuine day of no recorded sales, not a missing row. Therefore the quantity cannot determine whether CSV should replace it. The source ownership must be recorded separately.

**How we checked it.** Approval in conversation; no command executed.

**Next.** [Step 408](#step-408--approve-source-ownership-design).

### Step 408 — Approve source ownership design

**Before this step.** The existing DailySales table had no field to distinguish an imported total from a transaction-derived total. We approved a two-value source label and the default recorded for pre-existing rows.

**What we did.** No source edit; model design.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** The default protects the 17,568 existing demo history rows that were previously synchronized from application sales. It is an assumption about historical origin; it cannot recover provenance of any earlier manual CSV imports.

**How we checked it.** Design approval in conversation.

**Next.** [Step 409](#step-409--add-source-field-to-dailysales).

### Step 409 — Add source field to DailySales

**Before this step.** We added the source field to the existing DailySales model without changing the product/date uniqueness rule or the existing units and revenue fields. The new field is the basis for priority checks in services.

**What we did.** Updated or reviewed **intelligence/models.py** as part of the chapter's approved implementation.

**Code — `intelligence/models.py` (actual committed source)**

```python
source = models.CharField(
        max_length=10,
        choices=[
            ('recorded', 'Recorded Sales'),
            ('imported', 'CSV Import')
        ],
        default='recorded'
    )
```

**How the code works / why this approach.** Django CharField holds the origin label. The choices list provides readable options in model-backed forms; default recorded assigns an origin to rows when the schema is migrated. This field records provenance but does not itself enforce any overwrite policy.

**How we checked it.** User reported python manage.py check passed.

**Next.** [Step 410](#step-410--generate-migration).

### Step 410 — Generate migration

**Before this step.** After editing the model, the database still lacked the new source column. Django's migration generator compared the current model to its previous migration and produced an AddField operation.

**What we did.** Updated or reviewed **intelligence/migrations/0002_dailysales_source.py** as part of the chapter's approved implementation.

**Code — `intelligence/migrations/0002_dailysales_source.py` (actual committed source)**

```python
# Generated by Django 6.0.9 on 2026-11-13 16:28

from django.db import migrations, models


class Migration(migrations.Migration):

    dependencies = [
        ('intelligence', '0001_initial'),
    ]

    operations = [
        migrations.AddField(
            model_name='dailysales',
            name='source',
            field=models.CharField(choices=[('recorded', 'Recorded Sales'), ('imported', 'CSV Import')], default='recorded', max_length=10),
        ),
    ]
```

**How the code works / why this approach.** The dependency on 0001_initial ensures DailySales exists first. AddField adds the source column without dropping the table. The default recorded means older rows receive a value when the migration is applied.

**How we checked it.** User output: makemigrations intelligence created 0002_dailysales_source.py.

**Next.** [Step 411](#step-411--inspect-generated-migration).

### Step 411 — Inspect generated migration

**Before this step.** Before applying a schema change, we inspected the exact generated migration. The migration adds only the requested source field with the expected choices, default, and length.

**What we did.** Updated or reviewed **intelligence/migrations/0002_dailysales_source.py** as part of the chapter's approved implementation.

**Code — `intelligence/migrations/0002_dailysales_source.py` (actual committed source)**

```python
operations = [
        migrations.AddField(
            model_name='dailysales',
            name='source',
            field=models.CharField(choices=[('recorded', 'Recorded Sales'), ('imported', 'CSV Import')], default='recorded', max_length=10),
        ),
    ]
```

**How the code works / why this approach.** This review prevents accidental migrations from unrelated model edits. It also makes explicit that the default recorded is a backward-compatibility assumption, not proof that every old record came from transactions.

**How we checked it.** Migration content pasted and reviewed; no change made.

**Next.** [Step 412](#step-412--apply-migration-and-check-schema).

### Step 412 — Apply migration and check schema

**Before this step.** The new field was applied to the local SQLite database. Running checks and the migration dry-run confirmed the model and migration state matched.

**What we did.** Updated or reviewed **intelligence/migrations/0002_dailysales_source.py** as part of the chapter's approved implementation.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** Applying a migration updates schema, not service logic. Thus this step had to be followed by source-aware import and synchronization changes before priority could be relied on.

**How we checked it.** User output: No migrations to apply on subsequent migrate; Django check 0 issues; makemigrations --check --dry-run: No changes detected.

**Next.** [Step 413](#step-413--enforce-priority-in-services).

### Step 413 — Enforce priority in services

**Before this step.** The two existing services wrote the same product/date summary, creating an overwrite conflict. We changed the transaction sync to mark every row recorded, and the CSV import to skip any existing recorded row.

**What we did.** Updated or reviewed **intelligence/services.py** as part of the chapter's approved implementation.

**Code — `intelligence/services.py` (actual committed source)**

```python
def save_product_daily_sales(product_id, start_date, end_date):

    daily_data = get_product_daily_sales(
        product_id,
        start_date,
        end_date
    )

    product = Product.objects.get(pk=product_id)

    with transaction.atomic():

        for row in daily_data:
            DailySales.objects.update_or_create(
                product=product,
                date=row['date'],

                defaults={
                    'units_sold': row['units_sold'],
                    'gross_revenue': row['gross_revenue'],
                    'source': 'recorded'
                }

            )

    return len(daily_data)
```

**Code — `intelligence/services.py` (actual committed source)**

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
            product = products[row['sku']]

            existing = DailySales.objects.filter(
                product=product,
                date=row['date']
            ).first()

            if existing and existing.source == 'recorded':
                continue

            DailySales.objects.update_or_create(
                product=product,
                date=row['date'],
                defaults={
                    'units_sold': row['units_sold'],
                    'gross_revenue': row['gross_revenue'],
                    'source': 'imported'
                }
            )


    return len(validated_rows)
```

**How the code works / why this approach.** For synchronization, source recorded is placed in update_or_create defaults, so even a zero-sale day becomes recorded. For import, a filter(...).first() checks existing ownership; continue skips recorded rows. Imported rows are still inserted or updated atomically and marked imported. The return value remains the number of validated CSV rows, including skipped ones.

**How we checked it.** Covered by focused 29/29 tests and later full suite 427/427.

**Next.** [Step 414](#step-414--add-reconciliation-regression-tests).

### Step 414 — Add reconciliation regression tests

**Before this step.** Existing tests covered basic CSV import and daily aggregation but did not protect source priority. Six new tests exercise imported labels, recorded protection, recorded zero days, synchronization precedence, preservation outside the sync range, and zero-day ownership.

**What we did.** Updated or reviewed **intelligence/test_csv_validation.py and intelligence/test_daily_sales.py** as part of the chapter's approved implementation.

**Code — `intelligence/test_csv_validation.py` (actual committed source)**

```python
def test_import_marks_source_as_imported(self):
        csv_text = (
            'date,sku,units_sold,gross_revenue\n'
            '2025-11-10,IMP-001,3,150.00\n'
        )

        import_historical_daily_sales(csv_text)

        record = DailySales.objects.get()
        self.assertEqual(record.source, 'imported')

    def test_import_cannot_overwrite_recorded_sales(self):
        DailySales.objects.create(
            product=self.product,
            date=date(2025, 11, 10),
            units_sold=2,
            gross_revenue=Decimal('100.00'),
            source='recorded'
        )

        csv_text = (
            'date,sku,units_sold,gross_revenue\n'
            '2025-11-10,IMP-001,8,400.00\n'
        )

        import_historical_daily_sales(csv_text)

        record = DailySales.objects.get()

        self.assertEqual(record.units_sold, 2)
        self.assertEqual(
            record.gross_revenue,
            Decimal('100.00')
        )
        self.assertEqual(record.source, 'recorded')

    def test_import_cannot_overwrite_recorded_zero_day(self):
        DailySales.objects.create(
            product=self.product,
            date=date(2025, 11, 10),
            units_sold=0,
            gross_revenue=Decimal('0.00'),
            source='recorded'
        )

        csv_text = (
            'date,sku,units_sold,gross_revenue\n'
            '2025-11-10,IMP-001,8,400.00\n'
        )

        import_historical_daily_sales(csv_text)

        record = DailySales.objects.get()

        self.assertEqual(record.units_sold, 0)
        self.assertEqual(record.source, 'recorded')
```

**Code — `intelligence/test_daily_sales.py` (actual committed source)**

```python
def test_sync_replaces_imported_history(self):
        DailySales.objects.create(
            product=self.product,
            date=self.start_date,
            units_sold=8,
            gross_revenue=Decimal('400.00'),
            source='imported'
        )

        self.create_test_sale(
            1, self.start_date, 3
        )

        save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.start_date
        )

        record = DailySales.objects.get()

        self.assertEqual(record.units_sold, 3)
        self.assertEqual(
            record.gross_revenue,
            Decimal('150.00')
        )
        self.assertEqual(record.source, 'recorded')

    def test_sync_preserves_imported_history_outside_range(self):
        old_date = self.start_date - timedelta(days=1)

        DailySales.objects.create(
            product=self.product,
            date=old_date,
            units_sold=8,
            gross_revenue=Decimal('400.00'),
            source='imported'
        )

        save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.today
        )

        record = DailySales.objects.get(
            product=self.product,
            date=old_date
        )

        self.assertEqual(record.units_sold, 8)
        self.assertEqual(record.source, 'imported')

    def test_sync_marks_zero_sales_day_as_recorded(self):
        save_product_daily_sales(
            self.product.pk,
            self.start_date,
            self.start_date
        )

        record = DailySales.objects.get()

        self.assertEqual(record.units_sold, 0)
        self.assertEqual(record.source, 'recorded')
```

**How the code works / why this approach.** The tests create controlled Product and DailySales rows in isolated Django test databases. Assertions verify values and source labels, not merely record counts. The tests intentionally distinguish a zero recorded day from a CSV-only historical day.

**How we checked it.** Six added tests; focused suite increased to 29.

**Next.** [Step 415](#step-415--run-focused-verification).

### Step 415 — Run focused verification

**Before this step.** Before a long full-project regression, we ran migrations, Django checks, a migration dry-run, and the two focused intelligence test modules. This catches model/service mistakes quickly.

**What we did.** Updated or reviewed **No new source file; commands only.** as part of the chapter's approved implementation.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** The focused tests verify the new behavior without waiting for the entire app. Git status was also reviewed so unrelated local files would not be accidentally included in a later commit.

**How we checked it.** 29/29 passed in 9.138s; no system-check issues; no pending migrations.

**Next.** [Step 416](#step-416--audit-existing-history).

### Step 416 — Audit existing history

**Before this step.** A read-only shell query found 17,568 DailySales rows in the local development database. A follow-up grouped source query did not produce usable counts, so it is not valid to claim a verified recorded/imported breakdown.

**What we did.** No source edit; read-only database audit.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** A source migration cannot infer historical provenance from the values alone. The user stated they had no real CSV file and had not supplied a manual import; test CSV strings are isolated in test databases. The recorded default is appropriate for the known seeded history, subject to that assumption.

**How we checked it.** Observed total 17,568; source breakdown not confirmed.

**Next.** [Step 417](#step-417--run-full-regression-suite).

### Step 417 — Run full regression suite

**Before this step.** After the reconciliation implementation and focused checks, the user ran the complete Django test suite to catch regressions in purchasing, inventory, sales, and dashboards.

**What we did.** No source edit; verification only.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** The test runner created a separate test database, ran all discovered tests, and destroyed it afterward. A successful full run supports compatibility across the project but does not prove all real-world concurrency scenarios.

**How we checked it.** 427/427 passed in 617.512 seconds; 0 Django system-check issues.

**Next.** [Step 418](#step-418--clean-whitespace-and-push-source).

### Step 418 — Clean whitespace and push source

**Before this step.** The first Git attempt was blocked by git diff --cached --check due to trailing whitespace and extra EOF blank lines. A targeted Python cleanup changed only whitespace in five intended files, then focused tests were rerun before committing.

**What we did.** Updated or reviewed **Five source files listed in chapter overview.** as part of the chapter's approved implementation.

**Code — `intelligence/models.py` (actual committed source excerpt)**

```python
source = models.CharField(
        max_length=10,
        choices=[
            ('recorded', 'Recorded Sales'),
            ('imported', 'CSV Import')
        ],
        default='recorded'
    )
```

**How the code works / why this approach.** The cleanup removed trailing spaces/tabs and normalized the final blank lines without changing logic. Exact paths were staged rather than git add ., so purchasing.zip and reset_business_data_once.py remained untracked. The source repository was committed by the user, not by the journal assistant.

**How we checked it.** 29/29 focused tests passed after cleanup in 4.473s; commit ff0c68d pushed main; GitHub compare adb1355...ff0c68d verified exactly five files.

**Next.** [Step 419](#step-419--publish-study2-journal).

### Step 419 — Publish study-2 journal

**Before this step.** With source commit ff0c68d verified, the companion study journal can record the decisions, actual source code, verification evidence, and limitations before development proceeds.

**What we did.** Journal files: this chapter, README.md, STEP_INDEX.md, SOURCE_MAPPING.md, GLOSSARY.md, HANDOFF.md.

**Code.** No new code snippet was introduced in this step; the relevant changes appear in the implementation steps above.

**How the code works / why this approach.** The journal is maintained separately on study-2, while the source repository remains on main. This separation preserves a stable code checkpoint and a navigable learning record. The next chapter should focus on minimal forecasting-ready data preparation rather than inventing new application features.

**How we checked it.** Journal publication and remote verification are required for chapter closure.

**Next.** Continue with the next approved chapter after journal verification.

## Verification and handoff

- Django 6.0.9 migration `0002_dailysales_source.py` adds `source` with default `recorded`. The default is a provenance assumption for old records, not an audit.
- The user reported 17,568 existing DailySales rows. Source-specific counts were not successfully captured; do not invent them.
- Tests verify CSV cannot replace recorded or recorded-zero rows; synchronization replaces imported overlap and leaves imports outside its range untouched.
- `git diff --cached --check` initially failed; the user ran targeted whitespace cleanup and passed the staged check before committing. Unrelated untracked `purchasing.zip` and `reset_business_data_once.py` were not staged.
- **Remaining engineering limitation:** CSV import checks `source` and then updates in separate ORM calls; a concurrent synchronization/import race is not specifically tested. For the single-user academic SQLite scope, avoid adding unnecessary locking without a concrete requirement.
- **Next:** Module 7 Chapter 4, minimal forecasting input preparation; verify chapter scope before code. Use Rapid Development Mode: all related edits, commands, tests and whitespace cleanup in one batch. Source pushes remain user-owned; publish journal to `study-2` only.

## Recall questions

1. Why is a zero-unit recorded row not equivalent to missing historical data?
2. What happens when CSV and recorded totals disagree on the same product/date?
3. Why does the migration's default not prove the origin of every old row?
4. What does the importer return when some rows are skipped?
