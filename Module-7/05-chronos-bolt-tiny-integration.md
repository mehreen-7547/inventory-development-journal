# Module 7 — Chapter 5: Chronos-Bolt Tiny Demand Forecasting (Steps 425–429)

## Before this chapter
Chapter 4 supplied `get_forecasting_data(product_id, end_date)`: a chronological, gap-checked sequence of at least 60 daily unit-sales values. It did **not** generate predictions. Module 0 separately established that pretrained Amazon Chronos-Bolt Tiny could run on this Windows laptop's CPU, but that probe used synthetic data rather than Django's product history.

## What we built
A **read-only** forecasting service now passes a product's 60-day history to the pretrained `amazon/chronos-bolt-tiny` Transformer and returns 30 dated P10/P50/P90 forecasts. There is **no training, paid API, migration, stock change, UI, or demonstrated forecast accuracy** in this chapter.

**Verified source:** [commit `2c93e25`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/2c93e25), branch `main`, compared against `65ca45f`: two NEW files, 204 added lines (85 + 119), zero deletions.

## Step 425 — Implement the pretrained forecasting service

### Before this step
The history reader returned daily numbers but no model consumed them. We needed to connect the existing data contract to the already tested Chronos API, without building a new forecasting algorithm.

### What we did
**NEW — `intelligence/forecasting.py`** defines `MODEL_NAME`, `FORECAST_DAYS`, a cached model loader, and `forecast_product_demand(product_id, end_date)`.

**Code — `intelligence/forecasting.py` (model selection and cached loading):**
```python
MODEL_NAME = 'amazon/chronos-bolt-tiny'
FORECAST_DAYS = 30


@lru_cache(maxsize=1)
def load_forecasting_model():
    from chronos import BaseChronosPipeline

    return BaseChronosPipeline.from_pretrained(
        MODEL_NAME,
        device_map='cpu'
    )
```

**Explanation:** `BaseChronosPipeline.from_pretrained` loads existing weights, rather than training. `device_map='cpu'` avoids requiring a GPU. `@lru_cache(maxsize=1)` retains the loaded pipeline within a running Python process, avoiding a fresh load on every call; it does **not** persist predictions to the database. A first load may need network access to download weights; subsequent loads may use the local cache.

**Code — `intelligence/forecasting.py` (history → tensor → inference):**
```python
def forecast_product_demand(product_id, end_date):
    import torch

    history = get_forecasting_data(
        product_id,
        end_date
    )

    context = torch.tensor(
        history,
        dtype=torch.float32
    ).unsqueeze(0)

    model = load_forecasting_model()

    with torch.inference_mode():
        quantiles, _ = model.predict_quantiles(
            context,
            prediction_length=FORECAST_DAYS,
            quantile_levels=[0.1, 0.5, 0.9]
        )
```

**Explanation:** The earlier history reader enforces complete daily records before loading the model. `torch.tensor(..., dtype=torch.float32)` turns the Python list into numerical model input. `unsqueeze(0)` adds a batch dimension, producing shape `(1, 60)` for the default window: one product sequence with 60 days. `torch.inference_mode()` disables gradient tracking during prediction. The model requests three quantiles for each of the next 30 days: lower (P10), central (P50), and upper (P90). These are model-estimated quantiles, not guaranteed bounds.

**Code — `intelligence/forecasting.py` (validate and format model output):**
```python
    result = quantiles[0]

    if result.ndim == 3:
        result = result[0]

    if tuple(result.shape) != (FORECAST_DAYS, 3):
        raise ValueError('Unexpected AI forecast shape')

    if not torch.isfinite(result).all().item():
        raise ValueError('AI forecast contains invalid numbers')

    predictions = []

    for day, values in enumerate(result.tolist(), start=1):
        lower, middle, upper = values

        if not all(isfinite(v) for v in values):
            raise ValueError('AI forecast contains invalid numbers')

        lower = max(0.0, float(lower))
        middle = max(0.0, float(middle))
        upper = max(0.0, float(upper))

        # Keep displayed quantiles in increasing order.
        lower, middle, upper = sorted(
            [lower, middle, upper]
        )

        predictions.append({
            'date': end_date + timedelta(days=day),
            'p10': round(lower, 2),
            'p50': round(middle, 2),
            'p90': round(upper, 2)
        })
```

**Explanation:** The service expects 30 rows × 3 quantiles and rejects non-finite values such as NaN. It clips negative sales estimates to zero and sorts the displayed quantiles to keep them ordered. Dates begin **the day after** `end_date`. These are continuous numeric estimates; rounding to whole stock units is a separate future business decision. The source also returns a dictionary containing `product_id`, `model`, `history_days`, `forecast_days`, and `predictions`.

### How we checked it
The committed file was fetched from GitHub. Focused automated tests in Step 426 verify the shape and output contract. No Django endpoint or database forecast table was introduced.

### Next
Test the service without loading actual model weights on every test run.

## Step 426 — Add deterministic forecasting tests

### Before this step
A real model call can download weights and consume CPU and memory. Automated application tests should be fast, reproducible, and independent of that download.

### What we did
**NEW — `intelligence/test_forecasting.py`** adds five tests in `ForecastingTests(SimpleTestCase)`. `setUp` supplies a fixed end date and 60 values.

**Code — `intelligence/test_forecasting.py` (mock the model and inspect input):**
```python
    @patch('intelligence.forecasting.load_forecasting_model')
    @patch('intelligence.forecasting.get_forecasting_data')
    def test_generates_30_day_forecast(self, get_data, load_model):
        get_data.return_value = self.history

        output = torch.tensor(
            [[[3.0, 5.0, 7.0]] * 30]
        )
        load_model.return_value.predict_quantiles.return_value = (
            output, None
        )

        result = forecast_product_demand(
            1, self.end_date
        )

        self.assertEqual(result['forecast_days'], 30)
        self.assertEqual(len(result['predictions']), 30)
        self.assertEqual(result['predictions'][0]['p50'], 5.0)
```

**Explanation:** `@patch` replaces both the database-backed history reader and the model loader for this test. A known 30-day output makes assertions predictable. The same test checks first/last forecast dates, the input tensor shape `(1, 60)`, the 30-day prediction length, and quantile levels `[0.1, 0.5, 0.9]`.

**Code — `intelligence/test_forecasting.py` (reject invalid output):**
```python
        output = torch.zeros((1, 30, 3))
        output[0, 0, 1] = float('nan')

        load_model.return_value.predict_quantiles.return_value = (
            output, None
        )

        with self.assertRaisesRegex(
            ValueError, 'AI forecast contains invalid numbers'
        ):
            forecast_product_demand(1, self.end_date)
```

**Explanation:** The non-finite test ensures invalid numerical predictions are rejected instead of displayed. Other tests check clipping negative forecasts, rejecting an incorrect 29-day output, and stopping before model loading if the history reader reports missing dates. `SimpleTestCase` is appropriate because these mocked tests do not need database access.

### How we checked it
User terminal reported **11 tests found, 11 passed**, combining the five new forecasting tests with six Chapter 4 data-reader tests. Django system check reported zero issues; `makemigrations --check --dry-run` reported no changes. These are application tests with a **mocked model**, not a real inference benchmark.

### Next
Run the actual pretrained model in a separate probe.

## Step 427 — Verify real Chronos-Bolt Tiny inference

### Before this step
Mocks proved the Python integration logic but not that model weights and inference work in the current local environment.

### What we did
**EXISTING — `scripts/ai_probe.py` (no source modification)**:
```powershell
python scripts/ai_probe.py --model bolt
```

The probe loads `amazon/chronos-bolt-tiny` on CPU and forecasts 30 days from a **synthetic 90-day sequence**. It does not call `forecast_product_demand` or read a real product's Django history.

### How we checked it
User-provided terminal:
```text
Testing amazon/chronos-bolt-tiny ...
  WORKED: YES
  SECONDS TAKEN: 34.99
  PEAK MEMORY: 339.6 MB
  FORECAST ONLY: 0.01 seconds
  FIRST DAY P10/P50/P90: [7.37, 8.42, 9.39]
```

This demonstrates **local technical feasibility**, not forecast accuracy, not an end-to-end live product test, and not a guaranteed runtime for future products. The script wrote `ai_probe_results.json` locally; it was **not** included in the source commit.

### Next
Run the full regression suite and commit only intended files.

## Step 428 — Run regression and push the source

### Before this step
The new service and its tests existed locally but were untracked. We needed to verify existing functionality and commit a controlled change.

### What we did
**NEW source files:** `intelligence/forecasting.py`, `intelligence/test_forecasting.py`. The provided PowerShell workflow cleaned trailing whitespace/EOF, checked Django and migrations, ran `python manage.py test`, explicitly staged the two files, ran `git diff --cached --check`, committed, and pushed to `main`.

**Commands — source repository (relevant commands):**
```powershell
python manage.py test
git add -- intelligence/forecasting.py intelligence/test_forecasting.py
git diff --cached --check
git commit -m "Integrate Chronos-Bolt Tiny demand forecasting"
git push origin main
git status --short
git log -1 --oneline
```

### How we checked it
The user supplied `OK` from the full regression (full count and duration **not visible**), the staged summary **two files / 204 insertions**, and a successful push:
```text
[main 2c93e25] Integrate Chronos-Bolt Tiny demand forecasting
 2 files changed, 204 insertions(+)
 65ca45f..2c93e25  main -> main
?? purchasing.zip
?? reset_business_data_once.py
2c93e25 (HEAD -> main, origin/main) Integrate Chronos-Bolt Tiny demand forecasting
```
GitHub comparison independently confirmed **one commit, exactly two added files**, 85 and 119 lines. The unrelated untracked files were left untouched.

### Next
Publish and cross-link this chapter on journal branch `study-2`.

## Step 429 — Publish the development journal

### Before this step
The source was already pushed, but the educational journal had not yet recorded how the AI service was built, tested, and verified.

### What we did
**NEW — `Module-7/05-chronos-bolt-tiny-integration.md`**: this chapter documents the exact relevant source snippets and their filenames, the purpose of each step, test evidence, limitations, and the next chapter. **UPDATED —** `README.md`, `STEP_INDEX.md`, `SOURCE_MAPPING.md`, `GLOSSARY.md`, and `HANDOFF.md` on **`study-2`** only.

### How we checked it
Source was read at commit `2c93e25` before writing. Journal publication is confirmed by fetching the changed files from `study-2` after the updates.

### Next
Evaluate forecasting accuracy using a chronological holdout and compare against a simple moving-average baseline using MAE. Do not use forecast results for automatic stock changes or claim superior accuracy before this evaluation.

---

## Chapter recap and interview practice
**Flow:** `DailySales` → `get_forecasting_data` → 60-value tensor → pretrained Chronos-Bolt Tiny → 30 days × P10/P50/P90 → dated, read-only result.

**Explain independently:** Why do we use `unsqueeze(0)`? What does `torch.inference_mode()` do? Why are the tests mocked? How is a real-model feasibility probe different from an accuracy evaluation? Why are predictions not stock adjustments?

**Scope boundary:** No retraining, paid inference API, automatic reorder, MAE comparison, shortage detection, anomaly detection, or forecasting dashboard yet.
