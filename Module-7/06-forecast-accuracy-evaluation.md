# Module 7 — Chapter 6: Historical Forecast Accuracy Evaluation (Steps 430–435)

## Chapter introduction — what problem are we solving?
Chapter 5 could produce 30 days of demand forecasts from 60 consecutive historical days, but a functioning AI model is not automatically an accurate one. This chapter measures how far predictions differ from actual later sales and compares Chronos-Bolt Tiny with a simple, transparent benchmark. The evaluation is **read-only** and uses existing historical `DailySales` records. We do not retrain the model, modify inventory, or choose a production model based on a single small sample.

**Source verified:** [`b3b1051`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/b3b1051) on `main` against `2c93e25`: exactly two NEW files, 226 lines added (96 + 130), no other changed files.

### Workflow
`DailySales` → 90 contiguous days → first 60 days (model context) / final 30 days (hidden actuals) → Chronos 30-day P50 forecasts and fixed 7-day moving-average forecasts → two MAEs → lower-error method for that product and holdout.

**Important terminology:** The first 60 days are an **inference context**, not a local model-training set. The 30 held-out days are actual sales used only to score predictions. MAE measures average absolute error in **units sold per day**; smaller is better.

## Step 430 — Implement MAE calculation

### Before this step
The AI service returned predictions, but there was no numerical measure to tell whether they matched historical sales. We needed a simple metric that treats overprediction and underprediction symmetrically.

### What we did
**NEW — `intelligence/forecast_evaluation.py`**, `calculate_mae(actual, predicted)`.

**Code — `intelligence/forecast_evaluation.py`:**
```python
def calculate_mae(actual, predicted):

    if len(actual) != len(predicted) or not actual:
        raise ValueError('Invalid evaluation sequences')

    if not all(
        isfinite(value) and value >= 0
        for value in actual + predicted
    ):
        raise ValueError('Invalid evaluation values')

    errors = [
        abs(a - p)
        for a, p in zip(actual, predicted)
    ]

    return round(sum(errors) / len(errors), 4)
```

### Code explanation
- The first `if` rejects unequal sequence lengths or empty inputs. Without this check, `zip` would silently ignore extra entries and division by zero could occur.
- `isfinite` rejects `NaN` and infinity; the nonnegative check rejects impossible negative sales/forecast quantities.
- `zip(actual, predicted)` pairs the same day's observed and predicted values. `abs(a - p)` removes the sign so positive and negative errors do not cancel.
- Dividing the total absolute error by the number of days produces MAE. `round(..., 4)` makes the reported number readable; the underlying per-day estimates can be fractional.

For example, actual values `[10, 20, 30]` and predictions `[12, 18, 33]` have absolute errors `[2, 2, 3]`; MAE = `7/3 = 2.3333`.

### How we checked it
**NEW — `intelligence/test_forecast_evaluation.py`** asserts `2.3333` for that example, and checks rejection of mismatched lengths, empty sequences and a `NaN` prediction. These are deterministic unit tests.

### Next
Construct a time-ordered comparison that does not allow future sales into the forecast context.

## Step 431 — Add chronological holdout and moving-average baseline

### Before this step
Evaluating forecasts against the same days used to generate them would give a misleading result. We also needed a non-AI reference so that a low error could be interpreted against something meaningful.

### What we did
**NEW — `intelligence/forecast_evaluation.py`**, `evaluate_product_forecast(product_id, end_date)`. Constants set a 60-day context, 30-day test period, and seven-day baseline window.

**Code — `intelligence/forecast_evaluation.py` (split and cutoff):**
```python
HISTORY_DAYS = 60
TEST_DAYS = 30
BASELINE_DAYS = 7


def evaluate_product_forecast(product_id, end_date):

    # Read 90 consecutive days, ending on the evaluation date.
    sales = get_forecasting_data(
        product_id,
        end_date,
        days=HISTORY_DAYS + TEST_DAYS
    )

    history = sales[:HISTORY_DAYS]
    actual = sales[HISTORY_DAYS:]

    # The model must only see data before the test period.
    cutoff_date = end_date - timedelta(days=TEST_DAYS)

    forecast = forecast_product_demand(
        product_id,
        cutoff_date
    )
```

### Code explanation
- The existing `get_forecasting_data` reader requires a complete dated sequence, so missing dates are not silently treated as zero sales.
- Python slices separate the **first 60** values (history) from the **next 30** values (actual test outcomes). They preserve order.
- `cutoff_date` is 30 days before `end_date`. The call to `forecast_product_demand` uses this cutoff, causing that service to independently retrieve only the 60 earlier days. The test-period values are not passed into model inference.
- This is a single chronological holdout, **not** cross-validation across many windows. It measures one historical period per product.

**Code — `intelligence/forecast_evaluation.py` (align predictions and calculate baseline):**
```python
    predictions = forecast['predictions']

    if len(predictions) != TEST_DAYS:
        raise ValueError('Expected 30 AI predictions')

    for day, prediction in enumerate(predictions, start=1):
        expected_date = cutoff_date + timedelta(days=day)

        if prediction['date'] != expected_date:
            raise ValueError('AI prediction dates do not match')

    ai_values = [
        prediction['p50']
        for prediction in predictions
    ]

    # Predict each future day using the same last-seven-day
    # average available at the cutoff date.
    baseline_average = (
        sum(history[-BASELINE_DAYS:]) / BASELINE_DAYS
    )

    baseline_values = [
        baseline_average
        for _ in range(TEST_DAYS)
    ]

    ai_mae = calculate_mae(actual, ai_values)
    baseline_mae = calculate_mae(actual, baseline_values)
```

### Code explanation
- Forecast length and each date must match the holdout exactly; otherwise the MAE might compare different days.
- `p50` is the model's central forecast used for point-error evaluation. P10/P90 remain available in Chapter 5 but are not scored here.
- The baseline takes the average of the **last seven days before the cutoff**, then predicts that **same value for all 30 future days**. It does **not** update using actual held-out sales as each day passes. This avoids leakage and makes it a fair fixed-origin benchmark.
- Both sets of 30 predictions are compared against the **same** actual 30 days with `calculate_mae`.

**Code — `intelligence/forecast_evaluation.py` (result and winner):**
```python
    return {
        'product_id': product_id,
        'cutoff_date': cutoff_date,
        'test_start': cutoff_date + timedelta(days=1),
        'test_end': end_date,
        'history_days': HISTORY_DAYS,
        'test_days': TEST_DAYS,
        'ai_mae': ai_mae,
        'baseline_mae': baseline_mae,
        'better_model': (
            'chronos' if ai_mae < baseline_mae
            else 'moving_average' if baseline_mae < ai_mae
            else 'tie'
        )
    }
```

### Code explanation
The result records which product and dates were evaluated, both errors, and the lower-MAE method. The `better_model` field means **better on this historical test only**, not a globally selected model. This service returns data but does not write any database row or trigger purchasing.

### How we checked it
The source at `b3b1051` was fetched and checked for the exact cutoff and fixed-baseline logic. The mock tests in Step 432 verify the method selection and dates.

### Next
Test each branch of the evaluation without requiring an expensive pretrained model call in the test suite.

## Step 432 — Test the evaluation logic

### Before this step
The service combines history reading, forecast generation, date alignment, arithmetic, and result selection. Tests must isolate failures in that logic rather than depending on network downloads or variable model predictions.

### What we did
**NEW — `intelligence/test_forecast_evaluation.py`**, **CLASS — `ForecastEvaluationTests(SimpleTestCase)`**. Its `setUp` creates a deterministic 90-value series: sixty days of 10 units followed by thirty days of 12 units. `make_forecast` constructs mock dated P50 outputs.

**Code — `intelligence/test_forecast_evaluation.py` (known comparison and cutoff assertions):**
```python
    @patch('intelligence.forecast_evaluation.forecast_product_demand')
    @patch('intelligence.forecast_evaluation.get_forecasting_data')
    def test_chronos_beats_baseline(self, get_data, forecast):
        get_data.return_value = self.sales
        forecast.return_value = self.make_forecast()

        result = evaluate_product_forecast(
            1, self.end_date
        )

        self.assertEqual(result['ai_mae'], 0.0)
        self.assertEqual(result['baseline_mae'], 2.0)
        self.assertEqual(result['better_model'], 'chronos')

        get_data.assert_called_once_with(
            1, self.end_date, days=90
        )

        forecast.assert_called_once_with(
            1, self.end_date - timedelta(days=30)
        )
```

### Code explanation
- The decorators replace the **functions as imported into the evaluation module**. No actual model weights or database rows are needed for this test.
- In the controlled example, the AI predicts 12 for all 30 test days, so its MAE is 0. The seven-day baseline sees only 10s before the cutoff, so its MAE is 2.
- The final two assertions prove the history reader is asked for 90 days and the model receives a cutoff 30 days before the evaluation end. These checks protect the intended chronological split.

**Code — `intelligence/test_forecast_evaluation.py` (date mismatch):**
```python
        result = self.make_forecast()
        result['predictions'][0]['date'] = self.end_date
        forecast.return_value = result

        with self.assertRaisesRegex(
            ValueError, 'AI prediction dates do not match'
        ):
            evaluate_product_forecast(1, self.end_date)
```

### Code explanation
This test deliberately labels the first prediction with the wrong date. The service must raise `ValueError` instead of scoring misaligned days. Other tests cover a moving-average win, a tie, 29 predictions instead of 30, and missing history that stops the evaluation before calling the AI service.

### How we checked it
User terminal: Django system check **0 issues**, migration check **no changes**, and **19/19 focused tests passed** in 0.054 seconds. These 19 comprise eight new evaluation tests plus five Chapter 5 forecast tests and six Chapter 4 history tests. Mocked test outcomes do not prove actual model accuracy.

### Next
Run real model inference using the existing database's historical sales.

## Step 433 — Run real historical evaluation for five products

### Before this step
Mocked tests established that MAE and the comparison logic work. We still needed to run the pretrained model against real stored `DailySales` values and compare its predictions with historical outcomes.

### What we did
**No source files changed.** We first checked for products with 90 consecutive `DailySales` dates ending **2026-11-13** (window **2026-08-16 through 2026-11-13**). An initial command was invalid: it incorrectly imported `Product` from `inventory.models` and used `manage.py shell` with a multiline block that caused interactive indentation errors. Its printed zero eligible products was **not valid evidence**. We corrected the import to `catalog.models.Product` (confirmed by the `DailySales.product` foreign key to `catalog.Product`) and used a standalone Python process with `django.setup()`.

Five eligible products were identified, IDs **30–34**. We then invoked the actual `evaluate_product_forecast(product_id, end_date)` for each product in a read-only script.

**Code — terminal evaluation script (relevant invocation, not a committed file):**
```python
from django.utils import timezone
from catalog.models import Product
from intelligence.forecast_evaluation import evaluate_product_forecast

end_date = timezone.localdate()
product_ids = [30, 31, 32, 33, 34]

for product_id in product_ids:
    product = Product.objects.get(id=product_id)
    result = evaluate_product_forecast(product_id, end_date)
    print(product.name, result['ai_mae'],
          result['baseline_mae'], result['better_model'])
```

### Code explanation
The full command also set `DJANGO_SETTINGS_MODULE='config.settings'` and called `django.setup()` before importing Django models. Each product uses the same 90-day evaluation window, but its own sales history. `forecast_product_demand` loads the cached Chronos model and forecasts from the 60-day cutoff; the evaluation service compares the next 30 days with actual stored sales. The PyTorch Windows redirect warning was non-fatal.

### How we checked it — actual reported results

| Product (ID) | Chronos MAE | Fixed 7-day average MAE | Lower error |
|---|---:|---:|---|
| Classmate Pulse Notebook A4 180 Pages (30) | 1.1593 | 2.0143 | Chronos |
| Classmate Long Notebook 172 Pages (31) | 0.9977 | 1.2381 | Chronos |
| Navneet Youva Notebook A5 160 Pages (32) | 0.8833 | 1.0190 | Chronos |
| Cello Butterflow Blue Ball Pen Pack of 10 (33) | 0.7823 | 0.7000 | Moving average |
| Reynolds 045 Fine Carbure Pen Pack of 5 (34) | 0.5187 | 0.8000 | Chronos |

**Observed:** all five real evaluation calls succeeded; Chronos had lower MAE in **four of five** comparisons, while the baseline performed better for the Cello pen. These are user-supplied local results, **not** a reproducible committed evaluation-results artifact. The data are from a local historical/demo database; provenance and generalizability were not independently established. The sample is small and uses one holdout window, so do not claim statistically established superiority.

### Next
Run the full Django regression, check the staged diff, and push only the new evaluation files.

## Step 434 — Verify and commit the source

### Before this step
The evaluation logic was tested and tried against historical records, but the new files were still untracked. We needed a clean, limited source commit.

### What we did
**NEW — `intelligence/forecast_evaluation.py` (96 lines). NEW — `intelligence/test_forecast_evaluation.py` (130 lines).** The PowerShell block performed targeted whitespace/EOF cleanup, `python manage.py check`, `python manage.py makemigrations --check --dry-run`, `python manage.py test`, explicit staging, and a staged whitespace check.

**Code — PowerShell (essential commands):**
```powershell
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
git add -- intelligence/forecast_evaluation.py intelligence/test_forecast_evaluation.py
git diff --cached --check
git diff --cached --stat
git commit -m "Add historical forecast accuracy evaluation"
git push origin main
git status --short
git log -1 --oneline
```

### How we checked it
User output showed the full test run ended **`OK`** (the total number of tests and runtime were **not supplied**), two new files and 226 additions, and a successful push:
```text
[main b3b1051] Add historical forecast accuracy evaluation
 2 files changed, 226 insertions(+)
 create mode 100644 intelligence/forecast_evaluation.py
 create mode 100644 intelligence/test_forecast_evaluation.py
 2c93e25..b3b1051  main -> main
?? purchasing.zip
?? reset_business_data_once.py
b3b1051 (HEAD -> main, origin/main) Add historical forecast accuracy evaluation
```
GitHub comparison independently confirmed one new commit and **exactly two added files**. Unrelated local untracked files were not staged or changed.

### Next
Publish the journal and update indexes, source mapping, glossary, and handoff on the **separate** `study-2` branch.

## Step 435 — Publish Chapter 6 journal

### Before this step
The source commit was verified, but the study journal needed a detailed record of why the evaluation was designed this way, what code was actually committed, and what the observed results do and do not establish.

### What we did
**NEW — `Module-7/06-forecast-accuracy-evaluation.md`** (this chapter). **UPDATED —** `README.md`, `STEP_INDEX.md`, `SOURCE_MAPPING.md`, `GLOSSARY.md`, and `HANDOFF.md` on `study-2`. The source repo remains on `main`; no source code was changed by journal publication.

### How we checked it
The source files and compare results were fetched from GitHub before writing. Journal files are fetched again from `study-2` after publication to confirm the new chapter and cross-references exist.

### Next
Move to the next approved inventory intelligence workflow, likely stock-shortage prediction, after checking existing stock, incoming purchase orders, and supplier lead-time fields so recommendations match the actual project schema. Do not implement automated purchasing or introduce unapproved scope.

---

## Chapter review — explain it in your own words
1. **Why do we use 90 days?** The first 60 supply context and the next 30 are held out for scoring.
2. **Did we train Chronos?** No. We used a pretrained model for inference.
3. **What is MAE?** Average absolute difference between daily actual and predicted units.
4. **Why compare against a moving average?** To see whether a more complex model adds value beyond a simple forecast.
5. **Why use a fixed seven-day average for all 30 days?** Only data available at the forecast cutoff may be used.
6. **Does 4/5 wins prove AI is always better?** No; it is evidence for these products and this one historical window.
7. **Does the evaluation modify stock?** No; it is read-only.
