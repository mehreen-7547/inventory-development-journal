# Forecast comparison date correction

## Step 544 — Anchor comparison to available sales history

### Goal
Keep historical evaluation usable when the demonstration dataset stops before the current calendar week. Do not fabricate sales records or change the AI model.

### Diagnosis
On 24 November 2026 the page selected 22 November and skipped all 384 product-weeks. Calling the forecast directly for 15 November exposed a ValueError: Missing sales history for 2026-11-14. The data query showed dates from 13 November 2025 through 13 November 2026. The forecasting data service requires consecutive daily records, and the weekly evaluator catches errors and skips invalid weeks.

### MODIFIED — NEW logic

**File: `intelligence/views.py`**, inside `weekly_evaluation_dashboard`:

```python
    today = timezone.localdate()
    end_date = today - timedelta(days=today.weekday() + 1)
    latest_sales_date = DailySales.objects.filter(
        date__lte=end_date
    ).order_by('-date').values_list('date', flat=True).first()

    if latest_sales_date is not None:
        end_date = latest_sales_date - timedelta(
            days=(latest_sales_date.weekday() + 1) % 7
        )
```

First calculate the previous calendar Sunday. Query the newest sales date no later than that Sunday. The modulo expression moves a Friday back five days to Sunday, but leaves a Sunday unchanged. For this dataset the result is 8 November 2026. This selects a calendar boundary, not a guarantee of complete history for every product; the existing evaluator still validates individual weeks. If no sales exist, the previous calendar Sunday remains the fallback.

The cache key continues to include the evaluation end date, and POST recomputes the evaluation. The eight-week window, model, baseline calculations and database are unchanged.

### MODIFIED — NEW regression tests

**File: `intelligence/test_weekly_evaluation_view.py`**

Two new tests mock the current date and latest-sales query, log in a manager and check the response context. The key assertions are:

```python
        self.assertEqual(response.context['end_date'], date(2026, 11, 8))
```

```python
        self.assertEqual(response.context['end_date'], date(2026, 11, 22))
```

The first covers stale Friday history; the second ensures an available Sunday is preserved. Django ORM filtering, views and context follow familiar class concepts; historical cutoff arithmetic and regression tests are project-specific extensions.

### Verification
Source commits: `57caaea`, `695e2dc`. User pulled successfully, Django check passed and all four focused tests passed. Live browser evaluation ending 8 November: 48 products, 384 evaluated product-weeks, zero skipped. Mean weekly absolute errors: Chronos 3.40, previous 7-day average 3.23, predict zero 4.27. The average won. Full post-UI regression remains pending.

Step 543 was also visually verified after restarting the development server with --noreload; the direct Django receipt GET returned 200. No receipt was required for verification. The original milestone tag remains unchanged.
