# Module 7 — Chapter 9: Unusual Sales Pattern Detection

## Chapter introduction
Chapter 8 could recommend reorder quantities from demand forecasts. Chapter 9 adds a separate question: **Was a recorded sales day unusually high or low compared with the previous two weeks?** We chose a simple statistical detector rather than training or calling another AI model. It is explainable and inexpensive for a small-business academic project.

**Source checkpoint:** [`c5a8d43`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/c5a8d43), parent `2178fb6`; two NEW files, 363 additions. User verification: 17/17 focused tests, 489/489 full regression tests, zero Django system-check issues. This chapter covers **Steps 476–487**.

## Workflow
`DailySales` → fetch target dates and previous 14 days → skip missing records → calculate previous-14-day average → compare absolute units and percentage deviation → label unusually high/low → return explanations and skipped-day reasons. **No stock writes, database migrations, new AI model, alert persistence or UI.**

**Example:** baseline 10 units/day, actual 20: difference +10 and +100%, so flagged as high. Baseline 1 and actual 3: +200% but only +2 units, so not flagged. Baseline zero and actual eight: flagged by the minimum five-unit difference, with undefined percentage reported as `None`.

## Step 476 — Select the rolling-average anomaly method

### Before this step
The project already had Chronos demand forecasts but lacked a way to flag unusual observed daily sales.

### What we did
We compared a 14-day rolling-average method with Z-score detection and selected a simple statistical rule. This is not another AI model.

### Code — `intelligence/anomaly_detection.py`
```python
BASELINE_DAYS = 14
DEFAULT_THRESHOLD_PERCENT = 50
DEFAULT_MINIMUM_DIFFERENCE = 5
DEFAULT_ANALYSIS_DAYS = 30
```

### Code explanation
The constants define the previous 14 days, default 50% relative deviation, at least five units absolute deviation, and a 30-day analysis window. The two thresholds are initial configurable choices, not empirically calibrated. The code shown was implemented in the later service file; Step 476 was the user's design approval.

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 477.

## Step 477 — Create the anomaly detection service

### Before this step
The approved rule needed a reusable service, separate from views and templates.

### What we did
NEW FILE `intelligence/anomaly_detection.py` defines `detect_sales_anomalies` and reads `DailySales` without modifying records.

### Code — `intelligence/anomaly_detection.py`
```python
def detect_sales_anomalies(
    product_id,
    end_date,
    days=DEFAULT_ANALYSIS_DAYS,
    threshold_percent=DEFAULT_THRESHOLD_PERCENT,
    minimum_difference=DEFAULT_MINIMUM_DIFFERENCE
):
```

### Code explanation
The caller supplies product, cutoff date and optional thresholds. Defaults are kept at module level so the behavior is explicit. No Django model, migration, URL or view was changed. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 478.

## Step 478 — Validate date, analysis window and thresholds

### Before this step
Invalid dates, negative thresholds or non-finite percentages could produce misleading results.

### What we did
The function rejects non-date cutoffs, windows outside 1–30 days, invalid percentage thresholds and minimum unit differences below one.

### Code — `intelligence/anomaly_detection.py`
```python
if type(days) is not int or not 1 <= days <= 30:
    raise ValueError(
        'Analysis days must be between 1 and 30'
    )
```

### Code explanation
The complete source also rejects booleans for numeric settings, non-finite percentage values, and datetime objects in place of date. This keeps caller errors separate from legitimate no-anomaly results. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 479.

## Step 479 — Fetch the target and baseline sales records

### Before this step
Each target day needs the 14 calendar days immediately before it; repeated queries per day would be unnecessary.

### What we did
One filtered and date-ordered `DailySales` query covers the requested analysis window and the extra 14 baseline days.

### Code — `intelligence/anomaly_detection.py`
```python
start_date = end_date - timedelta(days=days - 1)
history_start = start_date - timedelta(
    days=BASELINE_DAYS
)

records = DailySales.objects.filter(
    product_id=product_id,
    date__gte=history_start,
    date__lte=end_date
).order_by('date')
```

### Code explanation
A dictionary keyed by `record.date` supports lookups for each calendar day. The database's existing unique product/date constraint prevents ambiguous duplicate rows. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 480.

## Step 480 — Skip missing target days and incomplete baselines

### Before this step
Missing data must not be treated as zero demand; a recorded zero remains a valid observation.

### What we did
The function records skipped days with a reason and analyzes only dates with a target record and 14 preceding records.

### Code — `intelligence/anomaly_detection.py`
```python
if actual_sales is None:
    skipped_days.append({
        'date': current_date,
        'reason': 'Missing daily sales record'
    })
    continue
```

### Code explanation
A separate history loop tests `history_date not in sales_by_date`; it skips that target if any baseline day is missing. `analyzed_days` counts only complete cases. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 481.

## Step 481 — Calculate the rolling average and dual thresholds

### Before this step
Percentage-only changes would overflag tiny differences, such as one unit rising to three.

### What we did
The function compares each actual value with the mean of the preceding 14 records and requires both 50% relative change and five units absolute difference by default.

### Code — `intelligence/anomaly_detection.py`
```python
average_sales = sum(history) / BASELINE_DAYS

difference = actual_sales - average_sales
absolute_difference = abs(difference)
```

### Code explanation
The baseline excludes the target day, so an unusual target cannot change its own expected average. A new baseline is calculated for each successive target date. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 482.

## Step 482 — Handle zero baseline and explain anomaly direction

### Before this step
Percentage change is undefined when the 14-day average is zero.

### What we did
When baseline is zero, percentage change is `None` and the absolute-unit threshold alone decides. Positive differences are unusually high; negative differences unusually low.

### Code — `intelligence/anomaly_detection.py`
```python
if average_sales == 0:
    percentage_change = None
    is_anomaly = (
        absolute_difference
        >= minimum_difference
    )
```

### Code explanation
A zero baseline and zero actual produce no alert. Each flagged record includes date, actual, rounded average, difference, percentage when defined, type and a plain-language explanation. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 483.

## Step 483 — Return a structured read-only analysis result

### Before this step
Other modules need a predictable result even when no days qualify.

### What we did
The service returns metadata, analyzed and skipped day counts, anomaly count and anomaly details.

### Code — `intelligence/anomaly_detection.py`
```python
return {
    'product_id': product_id,
    'start_date': start_date,
    'end_date': end_date,
    'baseline_days': BASELINE_DAYS,
    'threshold_percent': threshold_percent,
    'minimum_difference': minimum_difference,
    'analyzed_days': analyzed_days,
    'skipped_days': skipped_days,
    'anomaly_count': len(anomalies),
    'anomalies': anomalies,
    'method': 'statistical_rolling_average'
}
```

### Code explanation
The method field deliberately says statistical, not AI. No alerts are stored, stock is not adjusted, and no purchase order is created. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 484.

## Step 484 — Create mocked anomaly detection tests

### Before this step
We needed deterministic coverage without creating database rows or depending on the calendar.

### What we did
NEW FILE `intelligence/test_anomaly_detection.py`, CLASS `SalesAnomalyDetectionTests(SimpleTestCase)`, uses fixed dates, `SimpleNamespace` records and `patch` for the `DailySales` query.

### Code — `intelligence/test_anomaly_detection.py`
```python
class SalesAnomalyDetectionTests(SimpleTestCase):

    def setUp(self):
        self.end_date = date(2026, 11, 13)
        self.product_id = 30
```

### Code explanation
The mock returns controlled daily records to the real service. The 17 tests cover high/low anomalies, normal sales, threshold guards, zero baseline, missing records, rolling updates, custom thresholds and invalid inputs. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 485.

## Step 485 — Verify focused tests and the full project

### Before this step
A new intelligence service must not break earlier forecasting, sales, purchasing or dashboards.

### What we did
User ran Django checks, 17 focused anomaly tests and the complete project regression suite.

### Code — `terminal (PowerShell commands)`
```powershell
python manage.py check
python manage.py test intelligence.test_anomaly_detection
python manage.py test
```

### Code explanation
User terminal reported 17/17 focused tests passed in 0.007s and 489/489 full tests passed in 316.196s, with zero Django system-check issues. These are mocked anomaly tests; no real-database anomaly probe was reported. 

### How we checked it
The committed source was inspected at `c5a8d43`; user-reported verification was 17 focused and 489 full tests passing.

### Next
Proceed to Step 486.

## Step 486 — Commit and push only the two new files

### Before this step
The study journal must reference the exact source checkpoint, not uncommitted working files.

### What we did
User staged only the new anomaly service and test file, verified the staged diff, committed and pushed source commit `c5a8d43`.

### Code — `terminal (PowerShell commands)`
```powershell
git add intelligence/anomaly_detection.py intelligence/test_anomaly_detection.py
git diff --cached --check
git diff --cached --stat
git commit -m "Module 7 Chapter 9: Add unusual sales pattern detection"
git push origin main
git status --short
```

### Code explanation
Git output confirmed two new files, 363 insertions, parent `2178fb6`, and a successful push to `main`. `purchasing.zip` and `reset_business_data_once.py` remained untracked. 

### How we checked it
User-supplied Git push output verifies the source commit and exactly two new files.

### Next
Proceed to Step 487.

## Step 487 — Publish and verify the Chapter 9 journal

### Before this step
After the source push, the separate `study-2` journal needs a precise account of the code and test evidence.

### What we did
Create this chapter and update the README, step index, source mapping, glossary and handoff. Preserve the exact committed code below.

### Code — `study-2 journal`
```powershell
# Journal-only action: no source command required
```

### Code explanation
The journal describes approved choices, committed functions and user-reported verification. Its publication is separate from the source repository. 

### How we checked it
Journal publication and re-fetch verification are performed after the source is confirmed.

### Next
Review Chapter 9 completion and agree on the next remaining project module.

## Complete committed code — study reference

**NEW FILE — `intelligence/anomaly_detection.py`**
```python
from datetime import date, datetime, timedelta
from math import isfinite

from .models import DailySales


BASELINE_DAYS = 14
DEFAULT_THRESHOLD_PERCENT = 50
DEFAULT_MINIMUM_DIFFERENCE = 5
DEFAULT_ANALYSIS_DAYS = 30


def detect_sales_anomalies(
    product_id,
    end_date,
    days=DEFAULT_ANALYSIS_DAYS,
    threshold_percent=DEFAULT_THRESHOLD_PERCENT,
    minimum_difference=DEFAULT_MINIMUM_DIFFERENCE
):
    if (
        not isinstance(end_date, date)
        or isinstance(end_date, datetime)
    ):
        raise ValueError('Invalid end date')

    if type(days) is not int or not 1 <= days <= 30:
        raise ValueError(
            'Analysis days must be between 1 and 30'
        )

    if (
        not isinstance(threshold_percent, (int, float))
        or isinstance(threshold_percent, bool)
        or not isfinite(threshold_percent)
        or threshold_percent <= 0
    ):
        raise ValueError('Invalid percentage threshold')

    if (
        type(minimum_difference) is not int
        or minimum_difference < 1
    ):
        raise ValueError('Invalid minimum difference')

    start_date = end_date - timedelta(days=days - 1)
    history_start = start_date - timedelta(
        days=BASELINE_DAYS
    )

    records = DailySales.objects.filter(
        product_id=product_id,
        date__gte=history_start,
        date__lte=end_date
    ).order_by('date')

    sales_by_date = {
        record.date: record.units_sold
        for record in records
    }

    anomalies = []
    skipped_days = []
    analyzed_days = 0

    for day_number in range(days):
        current_date = start_date + timedelta(
            days=day_number
        )

        actual_sales = sales_by_date.get(current_date)

        if actual_sales is None:
            skipped_days.append({
                'date': current_date,
                'reason': 'Missing daily sales record'
            })
            continue

        history = []
        missing_history = False

        for previous_day in range(
            BASELINE_DAYS, 0, -1
        ):
            history_date = current_date - timedelta(
                days=previous_day
            )

            if history_date not in sales_by_date:
                missing_history = True
                break

            history.append(
                sales_by_date[history_date]
            )

        if missing_history:
            skipped_days.append({
                'date': current_date,
                'reason': 'Incomplete 14-day sales history'
            })
            continue

        analyzed_days += 1

        average_sales = sum(history) / BASELINE_DAYS

        difference = actual_sales - average_sales
        absolute_difference = abs(difference)

        if average_sales == 0:
            percentage_change = None

            is_anomaly = (
                absolute_difference
                >= minimum_difference
            )
        else:
            percentage_change = (
                difference / average_sales
            ) * 100

            is_anomaly = (
                absolute_difference >= minimum_difference
                and abs(percentage_change)
                >= threshold_percent
            )

        if not is_anomaly:
            continue

        if difference > 0:
            anomaly_type = 'unusually_high'
            explanation = (
                'Sales are unusually high compared '
                'with the previous 14 days'
            )
        else:
            anomaly_type = 'unusually_low'
            explanation = (
                'Sales are unusually low compared '
                'with the previous 14 days'
            )

        anomalies.append({
            'date': current_date,
            'actual_sales': actual_sales,
            'average_sales': round(average_sales, 2),
            'difference': round(difference, 2),
            'percentage_change': (
                round(percentage_change, 2)
                if percentage_change is not None
                else None
            ),
            'anomaly_type': anomaly_type,
            'explanation': explanation
        })

    return {
        'product_id': product_id,
        'start_date': start_date,
        'end_date': end_date,
        'baseline_days': BASELINE_DAYS,
        'threshold_percent': threshold_percent,
        'minimum_difference': minimum_difference,
        'analyzed_days': analyzed_days,
        'skipped_days': skipped_days,
        'anomaly_count': len(anomalies),
        'anomalies': anomalies,
        'method': 'statistical_rolling_average'
    }
```

**NEW FILE — `intelligence/test_anomaly_detection.py`**
```python
from datetime import date, timedelta
from types import SimpleNamespace
from unittest.mock import patch

from django.test import SimpleTestCase

from .anomaly_detection import detect_sales_anomalies


class SalesAnomalyDetectionTests(SimpleTestCase):

    def setUp(self):
        self.end_date = date(2026, 11, 13)
        self.product_id = 30

    def make_records(self, quantities):
        start_date = self.end_date - timedelta(
            days=len(quantities) - 1
        )

        return [
            SimpleNamespace(
                date=start_date + timedelta(days=index),
                units_sold=quantity
            )
            for index, quantity in enumerate(quantities)
        ]

    def run_detection(self, quantities, days=1, **kwargs):
        records = self.make_records(quantities)

        with patch(
            'intelligence.anomaly_detection.DailySales.objects.filter'
        ) as filter_sales:

            filter_sales.return_value.order_by.return_value = (
                records
            )

            return detect_sales_anomalies(
                self.product_id,
                self.end_date,
                days=days,
                **kwargs
            )

    def test_unusually_high_sales(self):
        result = self.run_detection([10] * 14 + [20])

        self.assertEqual(result['anomaly_count'], 1)
        self.assertEqual(
            result['anomalies'][0]['anomaly_type'],
            'unusually_high'
        )
        self.assertEqual(
            result['anomalies'][0]['percentage_change'],
            100
        )

    def test_unusually_low_sales(self):
        result = self.run_detection([20] * 14 + [5])

        self.assertEqual(result['anomaly_count'], 1)
        self.assertEqual(
            result['anomalies'][0]['anomaly_type'],
            'unusually_low'
        )

    def test_normal_sales_not_flagged(self):
        result = self.run_detection([10] * 14 + [12])

        self.assertEqual(result['anomaly_count'], 0)
        self.assertEqual(result['analyzed_days'], 1)

    def test_small_percentage_spike_not_flagged(self):
        result = self.run_detection([1] * 14 + [3])

        self.assertEqual(result['anomaly_count'], 0)

    def test_percentage_threshold_required(self):
        result = self.run_detection([20] * 14 + [26])

        self.assertEqual(result['anomaly_count'], 0)

    def test_zero_baseline_with_large_spike(self):
        result = self.run_detection([0] * 14 + [8])

        self.assertEqual(result['anomaly_count'], 1)
        self.assertIsNone(
            result['anomalies'][0]['percentage_change']
        )

    def test_zero_baseline_with_small_spike(self):
        result = self.run_detection([0] * 14 + [3])

        self.assertEqual(result['anomaly_count'], 0)

    def test_zero_baseline_and_zero_actual(self):
        result = self.run_detection([0] * 15)

        self.assertEqual(result['anomaly_count'], 0)

    def test_recorded_zero_sales_is_valid(self):
        result = self.run_detection([10] * 14 + [0])

        self.assertEqual(result['anomaly_count'], 1)
        self.assertEqual(result['analyzed_days'], 1)

    def test_missing_history_is_skipped(self):
        records = self.make_records([10] * 14 + [20])
        del records[5]

        with patch(
            'intelligence.anomaly_detection.DailySales.objects.filter'
        ) as filter_sales:
            filter_sales.return_value.order_by.return_value = (
                records
            )

            result = detect_sales_anomalies(
                self.product_id, self.end_date, days=1
            )

        self.assertEqual(result['analyzed_days'], 0)
        self.assertEqual(len(result['skipped_days']), 1)

    def test_missing_target_day_is_skipped(self):
        result = self.run_detection([10] * 14)

        self.assertEqual(result['analyzed_days'], 0)
        self.assertEqual(result['anomaly_count'], 0)

    def test_rolling_baseline_updates_each_day(self):
        result = self.run_detection(
            [10] * 14 + [30, 30],
            days=2
        )

        self.assertEqual(result['anomaly_count'], 2)
        self.assertGreater(
            result['anomalies'][1]['average_sales'],
            result['anomalies'][0]['average_sales']
        )

    def test_custom_thresholds(self):
        result = self.run_detection(
            [10] * 14 + [14],
            threshold_percent=30,
            minimum_difference=3
        )

        self.assertEqual(result['anomaly_count'], 1)

    def test_invalid_end_date(self):
        with self.assertRaisesRegex(
            ValueError, 'Invalid end date'
        ):
            detect_sales_anomalies(
                self.product_id, '2026-11-13'
            )

    def test_invalid_analysis_days(self):
        for days in [0, 31, True]:
            with self.subTest(days=days):
                with self.assertRaises(ValueError):
                    detect_sales_anomalies(
                        self.product_id,
                        self.end_date,
                        days=days
                    )

    def test_invalid_percentage_threshold(self):
        for threshold in [0, -10, float('nan'), True]:
            with self.subTest(threshold=threshold):
                with self.assertRaises(ValueError):
                    detect_sales_anomalies(
                        self.product_id,
                        self.end_date,
                        threshold_percent=threshold
                    )

    def test_invalid_minimum_difference(self):
        for difference in [0, -1, 2.5, True]:
            with self.subTest(difference=difference):
                with self.assertRaises(ValueError):
                    detect_sales_anomalies(
                        self.product_id,
                        self.end_date,
                        minimum_difference=difference
                    )
```

## Chapter review questions
1. Why is the target day excluded from its own baseline?
2. Why do we require both a percentage and a minimum-unit difference?
3. Why is a missing DailySales row different from a recorded zero?
4. What happens if all 14 baseline days contain zero sales?
5. Why does the next target day have a different rolling baseline?
6. Does this detector use AI? Does it place orders or update stock?
7. Why do mocked tests not establish how often real sales are falsely flagged?

## Limitations
The 50% and five-unit thresholds are project defaults, not empirically calibrated. The rolling average does not explicitly account for weekday effects, holidays, promotions or seasonality. It detects deviations but does not establish their causes. Days lacking complete recorded history are skipped rather than imputed. Tests are deterministic mocks; no real-database end-to-end anomaly run was supplied. Results are returned only; no interface, alert persistence, migrations or background tasks were implemented.
