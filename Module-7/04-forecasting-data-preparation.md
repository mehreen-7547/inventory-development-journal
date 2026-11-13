# Module 7 — Chapter 4: Forecasting Data Preparation

**Steps 420–424 · Source commit [65ca45f](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/65ca45f) · Branch `main`**

## Before this chapter
Chapter 3 established `DailySales` with daily product quantities, revenue and provenance (`recorded` or `imported`). The application can synchronize completed transactions and import validated CSV history. However, no dedicated service yet supplied a continuous demand sequence to the planned pretrained Chronos-Bolt Tiny forecasting model.

## Goal and boundaries
Prepare **at least 60 consecutive days** of daily `units_sold` for one product, ending on a chosen date. Preserve genuine zero-sales days, accept either source, and reject absent dates rather than inventing zero demand. This chapter **does not run Chronos, train a model, forecast 30 days, calculate accuracy, change stock, add tables, or build a UI**. The 30-day horizon and MAE/baseline evaluation remain planned for later work.

## Step 420 — Build a 60-day demand-history reader

### Before this step
`DailySales` existed, but the forecasting component had no reusable input function. Reading raw rows without checking date continuity could silently mislead the model.

### What we did
Created **NEW** `intelligence/forecasting_data.py`. The constant makes the minimum explicit, while the function validates its inputs, fetches a bounded date range, checks every calendar date, and returns a list of integer sales quantities in chronological order. The service reads records only.

### Code — `intelligence/forecasting_data.py` (NEW; selected actual source snippets)
```python
MIN_HISTORY_DAYS = 60


def get_forecasting_data(product_id, end_date, days=MIN_HISTORY_DAYS):

    if not isinstance(end_date, date) or isinstance(end_date, datetime):
        raise ValueError('Invalid end date')

    if type(days) is not int or days < MIN_HISTORY_DAYS:
        raise ValueError('At least 60 days of history are required')

    start_date = end_date - timedelta(days=days - 1)
```
**Walkthrough:** `MIN_HISTORY_DAYS` fixes the minimum at 60. The first guard accepts a calendar `date` but rejects a `datetime` or string; the second requires a real integer of 60 or more, rejecting booleans. Subtracting `days - 1` includes both the starting and ending dates, giving exactly 60 days by default.

```python
    records = DailySales.objects.filter(
        product_id=product_id,
        date__gte=start_date,
        date__lte=end_date
    ).order_by('date')

    sales_by_date = {
        record.date: record.units_sold
        for record in records
    }
```
**Walkthrough:** Django's ORM fetches only this product's rows within the window. `order_by('date')` requests chronological database results. The dictionary maps each date to its unit count for quick lookup. Revenue and source labels are not passed to the forecasting model; both imported and recorded summaries can contribute.

```python
    history = []
    current_date = start_date

    while current_date <= end_date:
        if current_date not in sales_by_date:
            raise ValueError(
                f'Missing sales history for {current_date}'
            )

        history.append(sales_by_date[current_date])
        current_date += timedelta(days=1)

    return history
```
**Walkthrough:** The loop visits every calendar date, not just dates returned by the database. Missing rows cause an explicit error, while a stored `units_sold=0` is retained as a valid zero. The resulting `history` is a plain list such as `[3, 0, 5, ...]`, suitable for later conversion into a model input tensor. This does not prove that the underlying historical transactions were complete.

### How we checked it
The dedicated tests in Step 421 exercise a full 60-day window, a zero day, missing dates, short windows, mixed source labels and read-only behavior. The focused run passed 35/35 tests, including prior intelligence tests.

### Next
Add automated tests that guard these assumptions.

## Step 421 — Add forecasting input tests

### Before this step
The reader's behavior needed repeatable evidence, especially for distinguishing missing history from a genuine zero.

### What we did
Created **NEW** `intelligence/test_forecasting_data.py` containing the `ForecastingDataTests(TestCase)` class and six test methods. `setUp` creates a supplier, product and 60 days of `DailySales` records; day 5 has zero sales, the first ten rows are imported and the rest recorded.

### Code — `intelligence/test_forecasting_data.py` (NEW; selected actual source snippets)
```python
        self.end_date = date(2026, 11, 12)
        self.start_date = self.end_date - timedelta(days=59)

        for day in range(60):
            DailySales.objects.create(
                product=self.product,
                date=self.start_date + timedelta(days=day),
                units_sold=0 if day == 5 else day + 1,
                gross_revenue=0,
                source='imported' if day < 10 else 'recorded'
            )
```
**Walkthrough:** The fixed end date makes tests independent of today's date. The loop creates exactly 60 distinct days. One known zero checks that zero demand is preserved; two source types check that the reader does not discard imported history.

```python
    def test_returns_60_values(self):
        history = get_forecasting_data(
            self.product.pk, self.end_date
        )
        self.assertEqual(len(history), 60)
        self.assertEqual(history[0], 1)
        self.assertEqual(history[-1], 60)
```
**Walkthrough:** This checks the window size and both ends of the sequence, catching reversed or shortened output.

```python
    def test_rejects_missing_date(self):
        DailySales.objects.filter(
            product=self.product,
            date=self.start_date + timedelta(days=10)
        ).delete()

        with self.assertRaisesRegex(ValueError, 'Missing sales history'):
            get_forecasting_data(self.product.pk, self.end_date)
```
**Walkthrough:** The test removes one daily record and expects the service to reject the incomplete series instead of silently filling the gap.

```python
    def test_does_not_change_sales(self):
        before = list(
            DailySales.objects.filter(
                product=self.product
            ).values_list('date', 'units_sold', 'source')
        )

        get_forecasting_data(self.product.pk, self.end_date)

        after = list(
            DailySales.objects.filter(
                product=self.product
            ).values_list('date', 'units_sold', 'source')
        )

        self.assertEqual(before, after)
```
**Walkthrough:** Comparing stored rows before and after verifies that preparing a forecast input does not edit sales quantities or provenance.

### Other tests in the actual file
- `test_preserves_zero_sales`: asserts the sixth quantity remains zero.
- `test_rejects_short_history`: rejects a requested 28-day window.
- `test_accepts_mixed_sources`: accepts the 60 rows regardless of their imported/recorded labels.

### How we checked it
All six new tests and 29 existing targeted intelligence tests passed together (35/35, 4.593 seconds).

### Next
Run Django checks and verify the project-wide regression before committing.

## Step 422 — Run targeted verification

### Before this step
Both new files existed locally but were untracked. No existing model or migration was changed.

### What we did
Ran `python manage.py check`, `python manage.py makemigrations --check --dry-run`, and `python manage.py test intelligence.test_forecasting_data intelligence.test_daily_sales intelligence.test_csv_validation`.

### Code — PowerShell (verification commands, not source files)
```powershell
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test intelligence.test_forecasting_data intelligence.test_daily_sales intelligence.test_csv_validation
git status --short
```
**Walkthrough:** The first command checks Django configuration. The second confirms model definitions need no migration. The third runs the new tests alongside related historical-data tests. `git status` identifies which files are new.

### How we checked it
User terminal: **0 Django issues**, **no changes detected** for migrations, **35/35 tests passed** in 4.593 seconds. Untracked `purchasing.zip` and `reset_business_data_once.py` were unrelated and deliberately excluded.

### Next
Run the full regression, then commit only the two new source files.

## Step 423 — Clean, run regression, commit and push

### Before this step
Targeted verification passed, but the change had not yet been published.

### What we did
Ran a targeted whitespace/EOF cleanup for only the two new Python files, staged their explicit paths, checked the staged diff, ran the full Django test suite and pushed to source `main`.

### Code — PowerShell (selected commands)
```powershell
git add intelligence/forecasting_data.py intelligence/test_forecasting_data.py
git diff --cached --check
git diff --cached --name-only
python manage.py test
git commit -m "Prepare 60-day demand history for AI forecasting"
git push origin main
```
**Walkthrough:** Explicit `git add` prevents unrelated untracked files from entering the commit. `git diff --cached --check` checks staged whitespace. The full test suite protects older functionality; the commit records the change and the push publishes it.

### How we checked it
User's terminal ended with `OK` and a successful push to `main`. The complete full-suite test count and duration were not supplied in the visible excerpt, so they are **not claimed** here. GitHub comparison `ff0c68d...65ca45f` independently confirmed **one commit, two new files, 130 additions, zero deletions**. Local status still listed only the two unrelated untracked files.

### Next
Publish the source-verified chapter to the separate `study-2` journal.

## Step 424 — Publish and hand off the chapter

### Before this step
The source was verified at commit `65ca45f`; the journal still ended at Step 419.

### What we did
Documented Steps 420–424 using selected **actual** source snippets, exact filenames, NEW markers, test evidence and limitations; updated the journal navigation, source mapping, glossary and handoff.

### Code — Journal paths (documentation, not application code)
```text
Module-7/04-forecasting-data-preparation.md
README.md
STEP_INDEX.md
SOURCE_MAPPING.md
GLOSSARY.md
HANDOFF.md
```
**Walkthrough:** The chapter explains the implementation; the other files make it discoverable and preserve the next-session checkpoint. The source code is not copied wholesale into the journal.

### How we checked it
The source commit and changed files were fetched directly from GitHub before writing this journal. Journal file publication is verified separately after writing.

### Next
Integrate **pretrained Chronos-Bolt Tiny inference**, producing a **30-day forecast** without training or paid APIs. First check existing Module 0 feasibility notes, model dependencies, local execution constraints and actual model API. Then add a moving-average baseline and MAE evaluation without overstating accuracy. Do not substitute a newly trained Random Forest for the agreed pretrained model.

## Chapter conclusion
Chapter 4 supplies continuous, read-only 60-day demand sequences. **No AI prediction has been implemented yet.** The future forecasting service will consume this output. A successful 60-day read is necessary for the chosen workflow, but does not by itself establish that historical data is representative or that predictions will be accurate.
