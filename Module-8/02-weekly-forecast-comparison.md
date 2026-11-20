# Module 8 — Chapter 2: Historical AI forecast comparison (Steps 511–532)

## Chapter introduction

Chapter 1 made urgent inventory decisions visible on the Manager homepage. Chapter 2 asks a different question: **does the AI forecast actually outperform simple alternatives?** The system evaluates the last eight completed seven-day periods for each product, comparing Chronos median demand predictions, the previous week's sales total (seven-day-average baseline), and a prediction of zero. Each method is scored against the same actual weekly total using absolute error.

The evaluation is retrospective: for every week, the AI receives the cutoff date immediately before that week. It reuses existing forecasting and sales-history services, adds no database tables or new models, and makes skipped weeks visible. A Manager runs it from `/intelligence/evaluation/`; the result is cached locally for 24 hours. The comparison is evidence, not a promise that one method always wins.

**Verified source:** [commit 78278d5](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/78278d5), eight changed files. **Reported checks:** 528 Django tests passed, no system-check issues, no pending migrations. **Live comparison:** 48 products, 336 evaluated product-weeks, 48 skipped; mean weekly absolute error AI **3.45**, previous-week baseline **3.29**, predict-zero **4.30**. The baseline was best on this run.

**How to study:** Read each step in order. Code blocks are targeted verbatim excerpts from source commit `78278d5`, not full-file replacements. Some excerpts omit surrounding context; do not paste them blindly into working files.

## Step 511 — Set the comparison boundaries

**Before this step.** We needed a repeatable comparison rather than judging AI by a single forecast.

**What we did.** Eight completed seven-day windows are evaluated against 60 days of earlier sales. These constants make the evaluation period explicit.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
WEEKS = 8
WEEK_DAYS = 7
HISTORY_DAYS = 60
```

**How we checked it.** The planned eight-week comparison was implemented and subsequently covered by automated tests.

**Next:** Step 512.

## Step 512 — Create the evaluation entry point

**Before this step.** The dashboard needed one service that could evaluate all products independently.

**What we did.** This plain Python service returns structured results; it does not render HTML or modify stock.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
def evaluate_weekly_forecasts(end_date):
    """Compare weekly totals without using future sales for predictions."""
    products = Product.objects.order_by('pk')
    rows = []
    skipped = 0
```

**How we checked it.** The eight-week service test later checked the returned row count.

**Next:** Step 513.

## Step 513 — Iterate through completed weeks

**Before this step.** A fair comparison requires the same historical week for every forecasting method.

**What we did.** The outer loop visits products and the inner loop moves from the oldest to newest week. `cutoff` is the day immediately before the week being predicted.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
for product in products:
    for weeks_ago in range(WEEKS - 1, -1, -1):
        week_end = end_date - timedelta(days=weeks_ago * WEEK_DAYS)
        cutoff = week_end - timedelta(days=WEEK_DAYS)
        week_start = cutoff + timedelta(days=1)
```

**How we checked it.** Tests later asserted eight evaluations for one product.

**Next:** Step 514.

## Step 514 — Separate past sales from actual outcomes

**Before this step.** Using the evaluation week's sales to generate its prediction would leak future information.

**What we did.** The first 60 days supply historical context; the final seven days are held aside as actual outcomes. The AI service is separately called with the earlier `cutoff`.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
sales = get_forecasting_data(
    product.pk, week_end, days=HISTORY_DAYS + WEEK_DAYS
)
history = sales[:HISTORY_DAYS]
actual = sales[HISTORY_DAYS:]
```

**How we checked it.** The service tests supply 67 daily values to exercise the split.

**Next:** Step 515.

## Step 515 — Request the AI forecast at the historical cutoff

**Before this step.** Forecasts must represent what could have been predicted before the evaluated week.

**What we did.** Passing `cutoff` avoids asking the model to forecast using the end of the evaluation week. The length check rejects incomplete seven-day forecasts.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
forecast = forecast_product_demand(product.pk, cutoff)
predictions = forecast['predictions'][:WEEK_DAYS]

if len(predictions) != WEEK_DAYS:
    raise ValueError('Expected seven AI predictions')
```

**How we checked it.** The successful mocked evaluation checked eight forecast calls.

**Next:** Step 516.

## Step 516 — Validate prediction dates

**Before this step.** A seven-value forecast is not useful if its dates belong to another week.

**What we did.** Each predicted date must be exactly one to seven days after the cutoff. Wrong-date results are skipped instead of silently compared.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
for day, prediction in enumerate(predictions, start=1):
    if prediction['date'] != cutoff + timedelta(days=day):
        raise ValueError('AI prediction dates do not match')
```

**How we checked it.** `test_rejects_wrong_prediction_dates` verifies that eight invalid weeks are skipped.

**Next:** Step 517.

## Step 517 — Reject invalid forecast quantities

**Before this step.** A missing, infinite, negative or malformed prediction should not distort accuracy statistics.

**What we did.** `p50` is the model's median daily prediction. `isfinite` excludes NaN and infinity, while the nonnegative check excludes impossible negative sales quantities.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
ai_values = [float(p['p50']) for p in predictions]

if not all(isfinite(v) and v >= 0 for v in ai_values):
    raise ValueError('Invalid AI predictions')
```

**How we checked it.** The final full test suite passed; there was no separate reported test for every numeric edge case.

**Next:** Step 518.

## Step 518 — Skip unusable product-weeks

**Before this step.** Some products may lack historical data or the model may be unavailable.

**What we did.** A known data/model failure skips only the current product-week and increments the visible skipped counter. Unexpected exception types are not swallowed.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
except (ValueError, TypeError, KeyError, RuntimeError,
        OSError, ImportError):
    skipped += 1
    continue
```

**How we checked it.** `test_missing_history_skips_week` confirmed eight skips and no AI calls when history retrieval fails.

**Next:** Step 519.

## Step 519 — Calculate the three weekly totals

**Before this step.** Daily predictions and actual daily sales must be reduced to comparable seven-day totals.

**What we did.** The baseline predicts the same total as the previous seven days; this is equivalent to multiplying that week's daily average by seven. The third method predicts zero.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
actual_total = sum(actual)
ai_total = sum(ai_values)
average_total = sum(history[-WEEK_DAYS:])
```

**How we checked it.** The successful mocked test checked all three methods.

**Next:** Step 520.

## Step 520 — Record absolute error per method

**Before this step.** An accuracy comparison needs a consistent distance between prediction and actual outcome.

**What we did.** Absolute error measures units missed regardless of whether a prediction is too high or too low. Zero error equals actual sales because sales quantities are nonnegative.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
'ai_error': round(abs(actual_total - ai_total), 2),
'average_error': abs(actual_total - average_total),
'zero_error': actual_total,
```

**How we checked it.** The mocked eight-week test expected AI error 0, baseline error 112, and zero error 672.

**Next:** Step 521.

## Step 521 — Keep each product-week inspectable

**Before this step.** A single headline score would hide which products or weeks were difficult.

**What we did.** Every row identifies the product, dates, three forecasts, actual total, and errors. This structure is reused directly by the dashboard template.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
rows.append({
    'product_id': product.pk,
    'product_name': product.name,
    'week_start': week_start,
    'week_end': week_end,
    'actual': actual_total,
    'ai': round(ai_total, 2),
    'average': average_total,
    'zero': 0,
    'ai_error': round(abs(actual_total - ai_total), 2),
    'average_error': abs(actual_total - average_total),
    'zero_error': actual_total,
})
```

**How we checked it.** The browser evaluation displayed per-product weekly rows.

**Next:** Step 522.

## Step 522 — Aggregate errors and choose a winner

**Before this step.** We needed an overall result without implying AI always wins.

**What we did.** The mean is total absolute error divided by evaluated product-weeks. Ties are retained; an empty evaluation has no winner.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
mean_errors = {
    method: round(totals[method] / count, 2) if count else None
    for method in methods
}

winners = (
    [method for method in methods
     if totals[method] == min(totals.values())]
    if count else []
)
```

**How we checked it.** The test asserted `best_methods == ['ai']` for a controlled AI-winning example.

**Next:** Step 523.

## Step 523 — Return counts and summary statistics

**Before this step.** The UI needs to explain how much evidence supports the result.

**What we did.** Counts distinguish products considered from valid product-weeks; skipped work is visible rather than silently omitted.

**File: NEW `intelligence/weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
return {
    'rows': rows,
    'product_count': products.count(),
    'evaluated_product_weeks': count,
    'skipped_product_weeks': skipped,
    'total_absolute_errors': totals,
    'mean_weekly_absolute_errors': mean_errors,
    'best_methods': winners,
    'weeks': WEEKS,
}
```

**How we checked it.** The live run reported 48 products, 336 evaluated product-weeks and 48 skipped.

**Next:** Step 524.

## Step 524 — Configure persistent evaluation caching

**Before this step.** Repeatedly loading a forecasting model for hundreds of historical evaluations is unnecessary.

**What we did.** Django stores cache files under `.django_cache`; `.gitignore` excludes that generated directory. This is a local cache, not a new database table.

**File: MODIFIED `config/settings.py` and `.gitignore`.**

**Code (exact relevant excerpt):**
```python
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.filebased.FileBasedCache',
        'LOCATION': BASE_DIR / '.django_cache',
        'TIMEOUT': 60 * 60 * 24,
    }
}
```

**How we checked it.** The cache view tests later confirmed a GET reused an earlier evaluation.

**Next:** Step 525.

## Step 525 — Create a manager-only evaluation view

**Before this step.** Only authorized managers should run and inspect the comparison.

**What we did.** The decorator protects the endpoint. The date expression chooses the latest completed Sunday, so the comparison does not include a partial current week.

**File: MODIFIED `intelligence/views.py`.**

**Code (exact relevant excerpt):**
```python
@role_required('manager')
def weekly_evaluation_dashboard(request):
    end_date = timezone.localdate()
    end_date -= timedelta(days=end_date.weekday() + 1)
```

**How we checked it.** The anonymous-user view test confirmed the evaluator was not invoked.

**Next:** Step 526.

## Step 526 — Run on POST and reuse cached results on GET

**Before this step.** Page refreshes should not automatically rerun the expensive evaluation.

**What we did.** GET reads any cached result; POST explicitly recalculates and stores it for 24 hours. The cache key changes when the completed-week cutoff changes.

**File: MODIFIED `intelligence/views.py`.**

**Code (exact relevant excerpt):**
```python
cache_key = f'weekly_forecast_evaluation_{end_date.isoformat()}'
evaluation = cache.get(cache_key)

if request.method == 'POST':
    evaluation = evaluate_weekly_forecasts(end_date)
    cache.set(cache_key, evaluation, 60 * 60 * 24)
```

**How we checked it.** `test_second_request_uses_cache` asserted one evaluator call across POST followed by GET.

**Next:** Step 527.

## Step 527 — Render the evaluation page

**Before this step.** The service result must reach the Django template.

**What we did.** `render` connects the request to the new HTML template and supplies the evaluation and cutoff date.

**File: MODIFIED `intelligence/views.py`.**

**Code (exact relevant excerpt):**
```python
return render(
    request,
    'intelligence/weekly_evaluation.html',
    {
        'evaluation': evaluation,
        'end_date': end_date
    }
)
```

**How we checked it.** The view test asserted successful HTTP 200 responses for authorized requests.

**Next:** Step 528.

## Step 528 — Register the dashboard URL

**Before this step.** Managers need a stable route to the evaluation page.

**What we did.** The named route is used by Django's `reverse` in tests and resolves to `/intelligence/evaluation/`.

**File: MODIFIED `intelligence/urls.py`.**

**Code (exact relevant excerpt):**
```python
path(
    'intelligence/evaluation/',
    views.weekly_evaluation_dashboard,
    name='weekly_evaluation_dashboard'
),
```

**How we checked it.** `reverse('weekly_evaluation_dashboard')` succeeded in the view tests.

**Next:** Step 529.

## Step 529 — Create the comparison template

**Before this step.** Managers need a button, summary and transparent per-week results.

**What we did.** The form triggers POST only on an explicit click. The template also displays mean errors, winning methods, skipped counts, and each product-week; the excerpt is limited to the essential action.

**File: NEW `intelligence/templates/intelligence/weekly_evaluation.html`.**

**Code (exact relevant excerpt):**
```html
<form method="post" class="mb-3">
    {% csrf_token %}
    <button type="submit" class="btn btn-primary">
        Run Forecast Comparison
    </button>
</form>
```

**How we checked it.** The live page rendered and the POST returned HTTP 200.

**Next:** Step 530.

## Step 530 — Test the historical evaluation service

**Before this step.** The model-dependent algorithm needs deterministic regression coverage.

**What we did.** Mocks supply predictable history and predictions. Three tests cover successful comparison, missing history and mismatched prediction dates without running the real AI model.

**File: NEW `intelligence/test_weekly_evaluation.py`.**

**Code (exact relevant excerpt):**
```python
self.assertEqual(result['evaluated_product_weeks'], 8)
self.assertEqual(result['skipped_product_weeks'], 0)
self.assertEqual(result['total_absolute_errors']['ai'], 0)
self.assertEqual(result['total_absolute_errors']['average'], 112)
self.assertEqual(result['total_absolute_errors']['zero'], 672)
self.assertEqual(result['best_methods'], ['ai'])
self.assertEqual(forecast.call_count, 8)
```

**How we checked it.** The final 528-test regression run passed.

**Next:** Step 531.

## Step 531 — Test permissions and cache behavior

**Before this step.** A correct evaluation algorithm still needs safe and predictable HTTP behavior.

**What we did.** The test checks that POST computes once and a later GET reuses the cache. A separate test ensures an anonymous request cannot run the evaluator.

**File: NEW `intelligence/test_weekly_evaluation_view.py`.**

**Code (exact relevant excerpt):**
```python
response1 = self.client.post(self.url)
response2 = self.client.get(self.url)

self.assertEqual(response1.status_code, 200)
self.assertEqual(response2.status_code, 200)
evaluate.assert_called_once()
```

**How we checked it.** Both view tests passed as part of the reported regression run.

**Next:** Step 532.

## Step 532 — Verify, stage, commit and push Chapter 2

**Before this step.** Implementation, tests and presentation must be checked together before publishing.

**What we did.** Only eight intended files were committed; unrelated `intelligence.zip` and `reset_business_data_once.py` remained untracked. The live comparison showed AI 3.45, previous-week baseline 3.29 and zero 4.30 mean weekly absolute error, so the baseline won this run.

**File: MODIFIED `.gitignore`, `config/settings.py`, `intelligence/urls.py`, `intelligence/views.py`; NEW `intelligence/weekly_evaluation.py`, `intelligence/test_weekly_evaluation.py`, `intelligence/test_weekly_evaluation_view.py`, `intelligence/templates/intelligence/weekly_evaluation.html`.**

**Code (exact relevant excerpt):**
```html
git diff --cached --check
git commit -m "Add historical AI forecast comparison dashboard"
git push origin main
```

**How we checked it.** User output: 528 tests passed, no system-check issues, no pending migrations; source commit `78278d5` pushed to `main`.

**Next:** Update and verify the study-2 journal navigation before Module 8 Chapter 3.

## Differences from class — NEW vs CLASS

**NEW vs CLASS 1 — Dedicated evaluation service.** `intelligence/weekly_evaluation.py` separates historical calculations from Django views, unlike the simpler view-centric exercises in class. This supports focused tests and reuse.

**NEW vs CLASS 2 — Mocked model and view tests.** `unittest.mock.patch` isolates model output and cache behavior; the class workflow relied more heavily on manually visiting pages. Mocks make edge cases reproducible.

**NEW vs CLASS 3 — Persistent local file cache.** The evaluation is expensive, so Django's file-based cache stores computed results for 24 hours; no paid service or extra database table is required.

## Chapter status

Chapter 2 source is implemented and published at `78278d5`. The reported full regression suite passed, and the live evaluation completed. Chapter 3 (README and five-minute demo) has not started.
