# Module 7 — Chapter 11: Making demand advice reliable and readable (Steps 499–507)

## Chapter introduction

The first dashboard worked, but real sparse sales exposed a decision problem: AI daily medians could be much lower than actual average demand. The manager also had to read technical tables and fractional item counts. This chapter documents the verified corrections in source commit [a5ad9ba](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/a5ad9ba6d21d468b21dd9626e02a48c77201dc46). These are **nine learning steps within one source commit**, not nine separate Git commits. The code below is deliberately limited to the relevant snippets. The earlier 498 steps remain unchanged.

**Before this chapter:** Step 498 verified the live dashboard. The source baseline was `b1c6063`. **After this chapter:** The user approved the new dashboard, reported **121/121 intelligence tests passed**, and then pushed the source commit above. A complete post-change project-wide test run was **not** supplied; the earlier 502-test full run occurred before the final presentation and shortage changes.

## Step 499 — Recognize why sparse sales can fool a daily forecast

**Before this step / Why.** Many products sell nothing on most days, so the model's typical daily value may be near zero even when occasional sales add up. Using that value alone for replenishment can understate what is needed. We decided to compare it with actual recent sales, without training another model.

**Files:** `intelligence/reorder_recommendation.py`, `intelligence/forecast_evaluation.py` (design review).

**Code:** No new code in this decision step.

**How we checked it.** The next steps add deterministic tests for the competing estimates.

**Next:** Step 500.

## Step 500 — Read 28 complete calendar days of sales

**Before this step / Why.** Missing records are not proof that zero units sold. A fair average needs every calendar date, including recorded zero-sales days.

**File: MODIFIED** `intelligence/reorder_recommendation.py`.

```python
if len(records) != days:
    return None
by_date = dict(records)
if any(end_date - timedelta(days=i) not in by_date for i in range(days)):
    return None
return sum(by_date.values()) / days
```

**Code walkthrough.** The first check requires the expected number of records. The second checks that each date exists, rather than trusting the count alone. Only then do we divide total units by calendar days.

**How we checked it.** `test_quality_rescue.py` covers recorded zeros and missing dates.

**Next:** Step 501.

## Step 501 — Choose the larger demand figure for reordering

**Before this step / Why.** Reordering based only on low AI medians could leave the business short. The reorder service now compares AI demand during supplier lead time with the recent 28-day average multiplied by that same lead time. The existing non-AI fallback remains for unavailable AI.

**File: MODIFIED** `intelligence/reorder_recommendation.py`.

**Relevant logic:** `lead_time_demand` is selected from the larger of the valid AI and recent-history values; `demand_basis` records which one won. This is a calculation change, not an automatic purchase order.

**How we checked it.** `test_quality_rescue.py` checks both cases: recent history larger and AI larger.

**Next:** Step 502.

## Step 502 — Compare forecasts against actual 30-day totals

**Before this step / Why.** Daily mean absolute error is useful, but managers also need to know whether the predicted **total units** were too high or too low. The existing historical holdout still uses 60 earlier days to predict the next 30 days.

**File: MODIFIED** `intelligence/forecast_evaluation.py`.

```python
actual_total = sum(actual)
ai_total = sum(ai_values)
baseline_total = sum(baseline_values)
```

**Code walkthrough.** These totals are compared with absolute differences in the returned `ai_total_error` and `baseline_total_error`. The existing daily MAE remains available.

**How we checked it.** `test_quality_rescue.py` checks total-unit error values.

**Next:** Step 503.

## Step 503 — Use the same conservative demand in shortage warnings

**Before this step / Why.** A reorder warning and a reassuring shortage message could disagree when they used different demand assumptions. The shortage simulation now checks each future day against the recent average and uses the larger demand. It still adds dated incoming deliveries and checks safety-stock breaches separately from complete stockouts.

**File: MODIFIED** `intelligence/shortage_prediction.py`.

```python
recent_average = get_recent_average(product_id, end_date)
# Inside the daily forecast loop:
demand = max(prediction['p50'], recent_average) if recent_average is not None else prediction['p50']
```

**Code walkthrough.** If complete recent history exists, `max` prevents a low daily AI value from making the shortage outlook overly optimistic. Without complete history, the validated AI figure remains. This aligns the **demand rule**, not necessarily the dates or thresholds of a reorder versus a stockout.

**How we checked it.** `test_final_wording.py` tests the higher-demand rule; existing `test_shortage_prediction.py` tests deliveries and shortage behavior.

**Next:** Step 504.

## Step 504 — Build a verdict-first manager summary

**Before this step / Why.** A long forecast table did not answer the first business question: should I reorder? The view now prepares a verdict, stock cover, safety stock, and concise messages before the template renders them.

**File: MODIFIED** `intelligence/views.py`.

```python
if cover is not None and cover > 90:
    verdict, tone = 'Overstocked', 'warning'
elif quantity is not None and quantity > 0:
    verdict, tone = 'Reorder now', 'danger'
elif quantity == 0:
    verdict, tone = 'Stock is healthy', 'success'
else:
    verdict, tone = 'Review needed', 'secondary'
```

**Code walkthrough.** The order of conditions matters: excess stock takes priority in the display. Missing advice is not falsely labelled healthy. The view passes the summary to the template without changing stock.

**How we checked it.** `test_presentation_rescue.py` covers overstocked, reorder, healthy and unavailable advice.

**Next:** Step 505.

## Step 505 — Present whole items and sensible sales periods

**Before this step / Why.** Fractional units such as 1.8 items are awkward for a small-business manager. Slow sellers are easier to understand weekly; faster sellers are easier to understand daily.

**File: MODIFIED** `intelligence/views.py`.

```python
def whole_units(value):
    return int(math.floor(float(value) + 0.5))

def item_label(n):
    return f"{n} {'unit' if n == 1 else 'units'}"
```

**Code walkthrough.** The first function rounds nonnegative display counts to whole items; the second selects the singular for exactly one unit. `selling_display` selects weekly wording below one unit per day, daily wording otherwise, and adds “Roughly 1 every N days” for very slow sales. Calculations retain their underlying precision.

**How we checked it.** `test_presentation_rescue.py` and `test_final_wording.py` cover selling-rate examples and singular/plural wording.

**Next:** Step 506.

## Step 506 — Move technical details behind readable cards

**Before this step / Why.** Managers should see the verdict and usable figures before raw model methods. The template now shows four tiles, 14-day sales guidance, historical reliability, shortage/incoming/anomaly messages, and two collapsed details sections.

**File: MODIFIED** `intelligence/templates/intelligence/dashboard.html`.

```django
<h3 class="h4 my-2">{{ decision.headline }}</h3>
<p class="mb-0">{{ decision.support }}</p>
<details class="mt-3">
  <summary class="text-primary">How is this calculated?</summary>
  <!-- The existing template places the technical explanation here. -->
</details>
```

**Code walkthrough.** The view owns the wording decisions; the template displays them. The actual page retains a collapsed 30-day forecast and a separate calculation section. No new JavaScript library, database table or automated stock action was added.

**How we checked it.** Presentation tests check the collapsed sections and technical-term placement; the user reviewed and approved the live page.

**Next:** Step 507.

## Step 507 — Repair regressions, verify, and publish the source

**Before this step / Why.** Several older tests asserted text from the previous design. Other failures came from a missing `math` import and a mocked date being formatted as a real date. These were corrected without deleting the behavioral coverage.

**Files: MODIFIED** `intelligence/test_dashboard.py`, `intelligence/test_reorder_recommendation.py`, `intelligence/test_shortage_prediction.py`, `intelligence/views.py`. **Files: NEW** `intelligence/test_quality_rescue.py`, `intelligence/test_presentation_rescue.py`, `intelligence/test_final_wording.py`.

**Code:** Tests use Django's test runner and controlled fixtures; the meaningful behaviors are verified in their named test methods, rather than pasting entire test files.

**How we checked it.** User terminal: `python manage.py test intelligence` ran **121 tests, OK**. `git diff --check` produced no formatting errors (only Git's LF/CRLF conversion notices). The user approved the interface. GitHub confirms source commit [a5ad9ba](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/a5ad9ba6d21d468b21dd9626e02a48c77201dc46) on `main`.

**Next:** Begin Module 8 after confirming its agreed requirements. No unrelated local ZIP or reset utility belongs in the source commit.

## Key terms

- **Calendar-day average:** Total recorded units divided by the number of consecutive dates, including recorded zeros.
- **Safety stock:** Minimum units to protect against shortages.
- **Days of cover:** Approximate days current stock could last at recent sales.
- **Holdout:** Past days hidden from the forecast and later used to check accuracy.
- **Read-only advice:** The system recommends an action but does not place an order or alter stock.
