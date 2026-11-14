# Module 7 — Chapter 8: Reorder Quantity Recommendations

## Chapter introduction
Chapter 7 projected future stock shortages but did not advise how many units to reorder. This chapter adds a **read-only planning service**: it estimates demand during the product's supplier lead time, protects the configured safety stock, and subtracts stock already on hand and reliably scheduled incoming units. Chronos-Bolt Tiny supplies the primary 30-day P50 demand forecast. A clearly labeled, non-AI historical-average fallback supports products with 7–59 consecutive days or an unavailable AI forecast. The service **never** creates purchase orders, updates stock, trains models or adds database tables.

**Verified source:** [`2178fb6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/2178fb6), parent `c226ceb`, two NEW files, 470 insertions. User's terminal: 472/472 full tests passed in 346.249s; Django system check zero issues. Chapter decisions and implementation Steps **451–475**.

## Architecture and calculation
`DailySales` → consecutive-sales fallback, or `forecast_product_demand` → AI P50 → lead-time demand; `Product` → on-hand, safety stock, lead time; `PurchaseOrderLine` → eligible scheduled incoming → `recommend_reorder_quantity` → structured result. All database operations are reads.

`Q = max(0, ceil(D_lead_time + safety_stock - on_hand - scheduled_incoming_within_lead_time))`.

**Example:** seven-day demand 35, safety stock 10, on-hand 20, scheduled eligible receipts 12 → 13 units. Orders due after day seven, overdue, or undated are shown separately but not subtracted.

## Step 451 — Choose forecast-based reorder calculation

### Before this step
We needed a reorder quantity that responds to expected demand rather than a fixed minimum or EOQ model.

### What we did
Approved demand over supplier lead time plus safety stock, less on-hand and eligible incoming; clamp negative quantities to zero.

### Code — `intelligence/reorder_recommendation.py`
```python
    required_quantity = (
        demand['lead_time_demand']
        + product.safety_stock
        - product.on_hand
        - incoming['scheduled_incoming']
    )
```

### Code explanation
The service eventually calculates `required_quantity` from these four components. This is planning advice, not an order. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 452.

## Step 452 — Limit recommendations to the 30-day horizon

### Before this step
Chronos produces only 30 future days; extrapolating beyond that would be an unsupported assumption.

### What we did
Approved lead times 1–30 days; return unavailable for zero or greater than 30.

### Code — `intelligence/reorder_recommendation.py`
```python
    if type(lead_time_days) is not int or not 1 <= lead_time_days <= 30:
        result['reason'] = (
            'Supplier lead time must be between 1 and 30 days'
        )
        return result
```

### Code explanation
The `lead_time_days` guard executes before the incoming-order query and forecast. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 453.

## Step 453 — Round up only the final reorder quantity

### Before this step
Daily P50 demand is fractional and should not be rounded independently.

### What we did
Approved `max(0, ceil(required_quantity))` once, after adding demand and buffer and subtracting stock and arrivals.

### Code — `intelligence/reorder_recommendation.py`
```python
    result['reorder_quantity'] = max(
        0, ceil(required_quantity)
    )
```

### Code explanation
`ceil` rounds up fractional final units; `max(0, ...)` prevents a negative recommendation. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 454.

## Step 454 — Count only incoming within lead time

### Before this step
A purchase order arriving after the lead-time horizon cannot prevent an earlier shortage.

### What we did
Approved eligible receipts dated after cutoff and on or before cutoff + lead time.

### Code — `intelligence/reorder_recommendation.py`
```python
        elif delivery_date <= cutoff_date:
            scheduled_incoming += remaining
        else:
            later_incoming += remaining
```

### Code explanation
`get_incoming_stock` separates scheduled versus later incoming, counting only the first in the formula. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 455.

## Step 455 — Exclude overdue and undated orders

### Before this step
An unconfirmed overdue receipt and an undated order should not be assumed to arrive.

### What we did
Approved separate overdue and unscheduled totals; neither reduces the recommended quantity.

### Code — `intelligence/reorder_recommendation.py`
```python
        if delivery_date is None:
            unscheduled_incoming += remaining
        elif delivery_date <= end_date:
            overdue_incoming += remaining
```

### Code explanation
`None` dates and dates on/before `end_date` have their own return fields. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 456.

## Step 456 — Keep the trigger tied to lead-time demand

### Before this step
The 30-day shortage projection and immediate reorder decision answer different questions.

### What we did
Approved calculating reorder only from demand over lead time, without triggering on later 30-day warnings.

### Code — `intelligence/reorder_recommendation.py`
```python
        if day <= lead_time_days:
            total_demand += demand
```

### Code explanation
The service does not call `predict_stock_shortage`; it computes its own lead-time demand. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 457.

## Step 457 — Provide a non-AI fallback for limited history

### Before this step
New products with fewer than 60 consecutive days cannot meet the Chronos input contract.

### What we did
Approved average-sales fallback for eligible shorter history and clear method labeling.

### Code — `intelligence/reorder_recommendation.py`
```python
    except Exception as error:
        result['ai_error'] = str(error)

        try:
            demand = calculate_fallback_demand(
                product_id, end_date, lead_time_days
            )
```

### Code explanation
`get_ai_lead_time_demand` calls Chronos; the main service attempts `calculate_fallback_demand` after an AI error. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 458.

## Step 458 — Require at least seven fallback days

### Before this step
One or two recorded days could give an unstable demand estimate.

### What we did
Approved 7–59 consecutive recorded days for the fallback.

### Code — `intelligence/reorder_recommendation.py`
```python
    if len(sales) < 7:
        raise ValueError(
            'At least 7 consecutive days of sales are required'
        )
```

### Code explanation
`get_fallback_sales` takes at most 59 rows and raises `ValueError` if fewer than seven consecutive days remain. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 459.

## Step 459 — Include recorded zero-sales days

### Before this step
A saved day with zero units is an observation, not missing history.

### What we did
Approved including zero values in the daily average.

### Code — `intelligence/reorder_recommendation.py`
```python
        sales.append(record.units_sold)
        expected_date -= timedelta(days=1)
```

### Code explanation
The fallback appends `record.units_sold`, including zero, then divides total by number of records. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 460.

## Step 460 — Stop at missing dates

### Before this step
A missing row must not be silently converted into zero sales.

### What we did
Approved the most recent uninterrupted run of dates ending at `end_date`.

### Code — `intelligence/reorder_recommendation.py`
```python
        if record.date != expected_date:
            break
```

### Code explanation
The helper compares each record date with `expected_date`, breaks at a gap and raises if fewer than seven days survive. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 461.

## Step 461 — Average all available consecutive days

### Before this step
Choosing only the latest week could discard useful observations.

### What we did
Approved all consecutive available days, capped at 59.

### Code — `intelligence/reorder_recommendation.py`
```python
    average_daily_demand = sum(sales) / len(sales)
```

### Code explanation
`sum(sales) / len(sales)` uses the complete returned run; no 7- or 14-day rolling override. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 462.

## Step 462 — Fallback when Chronos fails

### Before this step
A model-loading or execution error should not prevent an otherwise usable estimate.

### What we did
Approved non-AI fallback with the AI error recorded.

### Code — `intelligence/reorder_recommendation.py`
```python
    except Exception as error:
        result['ai_error'] = str(error)
```

### Code explanation
The main function catches AI exceptions, records `ai_error`, and tries the fallback. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 463.

## Step 463 — Return unavailable if both methods fail

### Before this step
Inventing a default reorder amount could cause unnecessary purchasing.

### What we did
Approved `status='unavailable'`, `reorder_quantity=None`, and an explanatory reason.

### Code — `intelligence/reorder_recommendation.py`
```python
        except (ValueError, TypeError) as fallback_error:
            result['reason'] = (
                'Unable to calculate reorder quantity: '
                + str(fallback_error)
            )
            return result
```

### Code explanation
If the fallback raises `ValueError` or `TypeError`, the service returns the initialized unavailable result. This was an approved design decision; the shown snippet comes from the later committed implementation, not a separate file change at this step.

### How we checked it
The decision was explicitly selected by the user; the completed code and tests were checked at the chapter close.

### Next
Continue to Step 464.

## Step 464 — Create the consecutive-sales helper

### Before this step
The fallback needs real daily sales records, not fabricated zeros.

### What we did
Created NEW `intelligence/reorder_recommendation.py` with `get_fallback_sales`.

### Code — `intelligence/reorder_recommendation.py`
```python
def get_fallback_sales(product_id, end_date):
    records = list(
        DailySales.objects.filter(
            product_id=product_id,
            date__lte=end_date
        ).order_by('-date')[:59]
    )
```

### Code explanation
The query filters by product and cutoff date, orders descending and slices to 59; the loop checks dates one by one. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 465.

## Step 465 — Calculate average-based lead-time demand

### Before this step
The sales helper returns history but not a lead-time estimate.

### What we did
Added `calculate_fallback_demand`, computing average units/day and multiplying by lead-time days.

### Code — `intelligence/reorder_recommendation.py`
```python
    return {
        'method': 'non_ai_fallback',
        'history_days': len(sales),
        'average_daily_demand': average_daily_demand,
        'lead_time_demand': (
            average_daily_demand * lead_time_days
        )
    }
```

### Code explanation
The result includes method, history days, average daily demand and lead-time demand. User's Django check passed. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 466.

## Step 466 — Validate the fallback lead-time range

### Before this step
A negative, zero or over-horizon lead time should not produce a recommendation.

### What we did
Added integer and 1–30-day validation in the completed file.

### Code — `intelligence/reorder_recommendation.py`
```python
    if type(lead_time_days) is not int or not 1 <= lead_time_days <= 30:
        raise ValueError(
            'Supplier lead time must be between 1 and 30 days'
        )
```

### Code explanation
The guard rejects booleans as well as out-of-range values by using `type(...) is not int`. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 467.

## Step 467 — Validate and sum Chronos P50 demand

### Before this step
Forecast outputs must align with expected dates and contain valid numbers.

### What we did
Added `get_ai_lead_time_demand` to verify 30 dated predictions and sum the first lead-time days.

### Code — `intelligence/reorder_recommendation.py`
```python
        if (
            not isinstance(demand, (int, float))
            or isinstance(demand, bool)
            or not isfinite(demand)
            or demand < 0
        ):
            raise ValueError('Invalid forecast demand')
```

### Code explanation
Each P50 must be finite, nonnegative, numeric and not a boolean. P10/P90 are not used for reorder quantity. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 468.

## Step 468 — Separate scheduled, overdue, undated and later receipts

### Before this step
Outstanding orders differ in reliability and timing.

### What we did
Added `get_incoming_stock` using remaining order-line quantities from ordered and partially received POs.

### Code — `intelligence/reorder_recommendation.py`
```python
    lines = PurchaseOrderLine.objects.filter(
        product_id=product_id,
        purchase_order__status__in=[
            'ordered',
            'partially_received'
        ],
        quantity_ordered__gt=F('quantity_received')
    ).select_related('purchase_order')
```

### Code explanation
`F('quantity_received')` filters fully received lines; `select_related` fetches parent delivery dates efficiently. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 469.

## Step 469 — Initialize a safe result and validate the request

### Before this step
The caller needs a consistent response even if forecasting is unavailable.

### What we did
Added `recommend_reorder_quantity` with date validation, product lookup and a full result dictionary.

### Code — `intelligence/reorder_recommendation.py`
```python
def recommend_reorder_quantity(product_id, end_date):
    if (
        not isinstance(end_date, date)
        or isinstance(end_date, datetime)
    ):
        raise ValueError('Invalid end date')

    product = Product.objects.get(id=product_id)
```

### Code explanation
An invalid end date raises `ValueError`; invalid lead time returns unavailable without inventing a quantity. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 470.

## Step 470 — Select AI or fallback and calculate the quantity

### Before this step
Demand and incoming totals must feed the approved arithmetic.

### What we did
Main service tries AI, falls back on failure, updates result and applies final ceiling.

### Code — `intelligence/reorder_recommendation.py`
```python
    result.update(demand)

    required_quantity = (
        demand['lead_time_demand']
        + product.safety_stock
        - product.on_hand
        - incoming['scheduled_incoming']
    )

    result['reorder_quantity'] = max(
        0, ceil(required_quantity)
    )
```

### Code explanation
It returns `method='ai_chronos'` or `method='non_ai_fallback'`, quantity, reason, incoming categories and any AI error. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 471.

## Step 471 — Create deterministic reorder service tests

### Before this step
Real model inference would make arithmetic tests slow and dependent on hardware.

### What we did
Created NEW `intelligence/test_reorder_recommendation.py`, CLASS `ReorderRecommendationTests(SimpleTestCase)`.

### Code — `intelligence/test_reorder_recommendation.py`
```python
    def test_incoming_within_lead_time(self):
        line = self.make_line(
            self.end_date + timedelta(days=7), 12
        )
        result = self.run_recommendation(lines=[line])
        self.assertEqual(result['scheduled_incoming'], 12)
        self.assertEqual(result['reorder_quantity'], 13)
```

### Code explanation
Mocks replace product lookup, forecast, purchase-order query and fallback. Eleven tests cover quantity, no reorder, rounding, timing, receipts, fallback, validation and unavailable status. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 472.

## Step 472 — Test fallback continuity and zero-sales days

### Before this step
The fallback needs separate coverage from the main recommendation function.

### What we did
Added CLASS `ReorderFallbackTests(SimpleTestCase)` with four tests.

### Code — `intelligence/test_reorder_recommendation.py`
```python
    def test_average_includes_zero_sales(self):
        records = self.make_records([5, 0, 0, 7, 0, 3, 0])

        with patch(
            'intelligence.reorder_recommendation.DailySales.objects.filter'
        ) as filter_sales:
            filter_sales.return_value.order_by.return_value.__getitem__.return_value = records
```

### Code explanation
The tests check zero-sales inclusion, interrupted history, fewer than seven days, and invalid lead time. 

### How we checked it
The code was fetched from source commit `2178fb6`. The full test suite passed 472/472; the new test file contains 15 tests.

### Next
Continue to Step 473.

## Step 473 — Run focused tests and Django checks

### Before this step
Before regression, the new functions needed fast deterministic verification.

### What we did
User ran `python manage.py check` and `python manage.py test intelligence.test_reorder_recommendation` as instructed.

### Code — `terminal (verification / Git commands)`
```powershell
python manage.py check
python manage.py test intelligence.test_reorder_recommendation
```

### Code explanation
The supplied full-suite result discovers 472 tests, 15 more than Chapter 7; no separate focused-test terminal transcript was supplied. 

### How we checked it
The user supplied the terminal result and/or Git output described above.

### Next
Continue to Step 474.

## Step 474 — Run full regression and inspect Git changes

### Before this step
New intelligence code must not break sales, purchasing or earlier forecasting.

### What we did
User reported 472/472 tests passed in 346.249 seconds and zero Django system-check issues; `git diff --check` was clean.

### Code — `terminal (verification / Git commands)`
```powershell
python manage.py test
git status --short
git diff --check
```

### Code explanation
`git status --short` showed only two new Python files plus two pre-existing unrelated untracked files. 

### How we checked it
The user supplied the terminal result and/or Git output described above.

### Next
Continue to Step 475.

## Step 475 — Commit and publish Chapter 8 source

### Before this step
The journal must refer to an immutable source checkpoint.

### What we did
User staged only two Python files, committed and pushed `2178fb6` to source `main`; 470 insertions, two files.

### Code — `terminal (verification / Git commands)`
```powershell
git add intelligence/reorder_recommendation.py intelligence/test_reorder_recommendation.py
git diff --cached --check
git diff --cached --stat
git commit -m "Module 7 Chapter 8: Add reorder quantity recommendations"
git push origin main
```

### Code explanation
`purchasing.zip` and `reset_business_data_once.py` remained untracked and untouched. The study journal is published separately on `study-2`. 

### How we checked it
The user supplied the terminal result and/or Git output described above.

### Next
Begin Chapter 9: Unusual Sales Pattern Detection after journal verification.

## Committed source code — complete reference

**NEW — `intelligence/reorder_recommendation.py`**
```python
from datetime import date, datetime, timedelta
from math import ceil, isfinite

from django.db.models import F

from catalog.models import Product
from purchasing.models import PurchaseOrderLine

from .forecasting import forecast_product_demand
from .models import DailySales


def get_fallback_sales(product_id, end_date):
    records = list(
        DailySales.objects.filter(
            product_id=product_id,
            date__lte=end_date
        ).order_by('-date')[:59]
    )

    sales = []
    expected_date = end_date

    for record in records:
        if record.date != expected_date:
            break

        sales.append(record.units_sold)
        expected_date -= timedelta(days=1)

    if len(sales) < 7:
        raise ValueError(
            'At least 7 consecutive days of sales are required'
        )

    return sales


def calculate_fallback_demand(
    product_id, end_date, lead_time_days
):
    if type(lead_time_days) is not int or not 1 <= lead_time_days <= 30:
        raise ValueError(
            'Supplier lead time must be between 1 and 30 days'
        )

    sales = get_fallback_sales(product_id, end_date)

    average_daily_demand = sum(sales) / len(sales)

    return {
        'method': 'non_ai_fallback',
        'history_days': len(sales),
        'average_daily_demand': average_daily_demand,
        'lead_time_demand': (
            average_daily_demand * lead_time_days
        )
    }


def get_ai_lead_time_demand(
    product_id, end_date, lead_time_days
):
    forecast = forecast_product_demand(
        product_id, end_date
    )

    predictions = forecast['predictions']

    if len(predictions) != 30:
        raise ValueError('Expected 30 forecast days')

    total_demand = 0

    for day, prediction in enumerate(predictions, start=1):
        expected_date = end_date + timedelta(days=day)

        if prediction['date'] != expected_date:
            raise ValueError('Invalid forecast dates')

        demand = prediction['p50']

        if (
            not isinstance(demand, (int, float))
            or isinstance(demand, bool)
            or not isfinite(demand)
            or demand < 0
        ):
            raise ValueError('Invalid forecast demand')

        if day <= lead_time_days:
            total_demand += demand

    return {
        'method': 'ai_chronos',
        'history_days': forecast['history_days'],
        'average_daily_demand': None,
        'lead_time_demand': total_demand
    }


def get_incoming_stock(
    product_id, end_date, lead_time_days
):
    cutoff_date = end_date + timedelta(
        days=lead_time_days
    )

    lines = PurchaseOrderLine.objects.filter(
        product_id=product_id,
        purchase_order__status__in=[
            'ordered',
            'partially_received'
        ],
        quantity_ordered__gt=F('quantity_received')
    ).select_related('purchase_order')

    scheduled_incoming = 0
    overdue_incoming = 0
    unscheduled_incoming = 0
    later_incoming = 0

    for line in lines:
        remaining = (
            line.quantity_ordered - line.quantity_received
        )

        delivery_date = (
            line.purchase_order.expected_delivery_date
        )

        if delivery_date is None:
            unscheduled_incoming += remaining
        elif delivery_date <= end_date:
            overdue_incoming += remaining
        elif delivery_date <= cutoff_date:
            scheduled_incoming += remaining
        else:
            later_incoming += remaining

    return {
        'scheduled_incoming': scheduled_incoming,
        'overdue_incoming': overdue_incoming,
        'unscheduled_incoming': unscheduled_incoming,
        'later_incoming': later_incoming
    }


def recommend_reorder_quantity(product_id, end_date):
    if (
        not isinstance(end_date, date)
        or isinstance(end_date, datetime)
    ):
        raise ValueError('Invalid end date')

    product = Product.objects.get(id=product_id)

    lead_time_days = product.lead_time_days

    result = {
        'product_id': product.id,
        'product_name': product.name,
        'on_hand': product.on_hand,
        'safety_stock': product.safety_stock,
        'lead_time_days': lead_time_days,
        'status': 'unavailable',
        'method': None,
        'history_days': None,
        'average_daily_demand': None,
        'lead_time_demand': None,
        'scheduled_incoming': 0,
        'overdue_incoming': 0,
        'unscheduled_incoming': 0,
        'later_incoming': 0,
        'reorder_quantity': None,
        'reason': None,
        'ai_error': None
    }

    if type(lead_time_days) is not int or not 1 <= lead_time_days <= 30:
        result['reason'] = (
            'Supplier lead time must be between 1 and 30 days'
        )
        return result

    incoming = get_incoming_stock(
        product_id, end_date, lead_time_days
    )

    result.update(incoming)

    try:
        demand = get_ai_lead_time_demand(
            product_id, end_date, lead_time_days
        )
    except Exception as error:
        result['ai_error'] = str(error)

        try:
            demand = calculate_fallback_demand(
                product_id, end_date, lead_time_days
            )
        except (ValueError, TypeError) as fallback_error:
            result['reason'] = (
                'Unable to calculate reorder quantity: '
                + str(fallback_error)
            )
            return result

    result.update(demand)

    required_quantity = (
        demand['lead_time_demand']
        + product.safety_stock
        - product.on_hand
        - incoming['scheduled_incoming']
    )

    result['reorder_quantity'] = max(
        0, ceil(required_quantity)
    )

    result['status'] = 'available'

    if result['reorder_quantity'] == 0:
        result['reason'] = 'No reorder required'
    else:
        result['reason'] = 'Reorder recommended'

    return result
```

**NEW — `intelligence/test_reorder_recommendation.py`**
```python
from datetime import date, timedelta
from types import SimpleNamespace
from unittest.mock import patch

from django.test import SimpleTestCase

from .reorder_recommendation import (
    calculate_fallback_demand,
    get_fallback_sales,
    recommend_reorder_quantity
)


class ReorderRecommendationTests(SimpleTestCase):

    def setUp(self):
        self.end_date = date(2026, 11, 13)
        self.product = SimpleNamespace(
            id=30,
            name='Test Notebook',
            on_hand=20,
            safety_stock=10,
            lead_time_days=7
        )

    def make_forecast(self, demand=5):
        return {
            'history_days': 60,
            'predictions': [
                {
                    'date': self.end_date + timedelta(days=day),
                    'p50': demand
                }
                for day in range(1, 31)
            ]
        }

    def make_line(self, delivery_date, ordered, received=0):
        return SimpleNamespace(
            quantity_ordered=ordered,
            quantity_received=received,
            purchase_order=SimpleNamespace(
                expected_delivery_date=delivery_date
            )
        )

    def run_recommendation(
        self, demand=5, lines=None, ai_error=None
    ):
        with patch(
            'intelligence.reorder_recommendation.Product.objects.get',
            return_value=self.product
        ), patch(
            'intelligence.reorder_recommendation.forecast_product_demand'
        ) as forecast, patch(
            'intelligence.reorder_recommendation.PurchaseOrderLine.objects.filter'
        ) as filter_lines, patch(
            'intelligence.reorder_recommendation.calculate_fallback_demand'
        ) as fallback:

            filter_lines.return_value.select_related.return_value = (
                lines if lines is not None else []
            )

            if ai_error is None:
                forecast.return_value = self.make_forecast(demand)
            else:
                forecast.side_effect = ai_error
                fallback.return_value = {
                    'method': 'non_ai_fallback',
                    'history_days': 15,
                    'average_daily_demand': 6,
                    'lead_time_demand': 42
                }

            return recommend_reorder_quantity(
                self.product.id, self.end_date
            )

    def test_ai_reorder_quantity(self):
        result = self.run_recommendation()
        self.assertEqual(result['reorder_quantity'], 25)
        self.assertEqual(result['method'], 'ai_chronos')

    def test_no_reorder_when_stock_is_sufficient(self):
        self.product.on_hand = 100
        result = self.run_recommendation()
        self.assertEqual(result['reorder_quantity'], 0)

    def test_rounds_only_final_quantity(self):
        result = self.run_recommendation(demand=1.05)
        self.assertEqual(result['reorder_quantity'], 0)

        self.product.on_hand = 0
        result = self.run_recommendation(demand=1.05)
        self.assertEqual(result['reorder_quantity'], 18)

    def test_incoming_within_lead_time(self):
        line = self.make_line(
            self.end_date + timedelta(days=7), 12
        )
        result = self.run_recommendation(lines=[line])
        self.assertEqual(result['scheduled_incoming'], 12)
        self.assertEqual(result['reorder_quantity'], 13)

    def test_late_delivery_is_excluded(self):
        line = self.make_line(
            self.end_date + timedelta(days=8), 12
        )
        result = self.run_recommendation(lines=[line])
        self.assertEqual(result['later_incoming'], 12)
        self.assertEqual(result['reorder_quantity'], 25)

    def test_overdue_and_undated_are_excluded(self):
        lines = [
            self.make_line(self.end_date, 10),
            self.make_line(None, 8)
        ]
        result = self.run_recommendation(lines=lines)
        self.assertEqual(result['overdue_incoming'], 10)
        self.assertEqual(result['unscheduled_incoming'], 8)
        self.assertEqual(result['reorder_quantity'], 25)

    def test_partial_receipt_uses_remaining(self):
        line = self.make_line(
            self.end_date + timedelta(days=3),
            ordered=20,
            received=12
        )
        result = self.run_recommendation(lines=[line])
        self.assertEqual(result['scheduled_incoming'], 8)
        self.assertEqual(result['reorder_quantity'], 17)

    def test_ai_failure_uses_fallback(self):
        result = self.run_recommendation(
            ai_error=RuntimeError('Model unavailable')
        )
        self.assertEqual(result['method'], 'non_ai_fallback')
        self.assertEqual(result['reorder_quantity'], 32)
        self.assertIn('Model unavailable', result['ai_error'])

    def test_invalid_lead_time(self):
        self.product.lead_time_days = 31
        result = self.run_recommendation()
        self.assertEqual(result['status'], 'unavailable')
        self.assertIsNone(result['reorder_quantity'])

    def test_invalid_end_date(self):
        with self.assertRaisesRegex(ValueError, 'Invalid end date'):
            recommend_reorder_quantity(30, '2026-11-13')

    def test_invalid_forecast_uses_fallback(self):
        with patch(
            'intelligence.reorder_recommendation.Product.objects.get',
            return_value=self.product
        ), patch(
            'intelligence.reorder_recommendation.forecast_product_demand',
            return_value={'history_days': 60, 'predictions': []}
        ), patch(
            'intelligence.reorder_recommendation.PurchaseOrderLine.objects.filter'
        ) as filter_lines, patch(
            'intelligence.reorder_recommendation.calculate_fallback_demand',
            side_effect=ValueError('Not enough history')
        ):
            filter_lines.return_value.select_related.return_value = []

            result = recommend_reorder_quantity(
                30, self.end_date
            )

        self.assertEqual(result['status'], 'unavailable')
        self.assertIsNone(result['reorder_quantity'])
        self.assertIn('Not enough history', result['reason'])


class ReorderFallbackTests(SimpleTestCase):

    def setUp(self):
        self.end_date = date(2026, 11, 13)

    def make_records(self, quantities):
        return [
            SimpleNamespace(
                date=self.end_date - timedelta(days=index),
                units_sold=quantity
            )
            for index, quantity in enumerate(quantities)
        ]

    def test_average_includes_zero_sales(self):
        records = self.make_records([5, 0, 0, 7, 0, 3, 0])

        with patch(
            'intelligence.reorder_recommendation.DailySales.objects.filter'
        ) as filter_sales:
            filter_sales.return_value.order_by.return_value.__getitem__.return_value = records

            result = calculate_fallback_demand(
                30, self.end_date, 7
            )

        self.assertEqual(result['history_days'], 7)
        self.assertAlmostEqual(
            result['lead_time_demand'], 15
        )

    def test_missing_day_breaks_history(self):
        records = self.make_records([2] * 7)
        records[3].date -= timedelta(days=1)

        with patch(
            'intelligence.reorder_recommendation.DailySales.objects.filter'
        ) as filter_sales:
            filter_sales.return_value.order_by.return_value.__getitem__.return_value = records

            with self.assertRaisesRegex(
                ValueError, 'At least 7 consecutive days'
            ):
                get_fallback_sales(30, self.end_date)

    def test_fallback_requires_seven_days(self):
        records = self.make_records([2] * 6)

        with patch(
            'intelligence.reorder_recommendation.DailySales.objects.filter'
        ) as filter_sales:
            filter_sales.return_value.order_by.return_value.__getitem__.return_value = records

            with self.assertRaises(ValueError):
                get_fallback_sales(30, self.end_date)

    def test_fallback_rejects_invalid_lead_time(self):
        with self.assertRaisesRegex(
            ValueError, 'between 1 and 30 days'
        ):
            calculate_fallback_demand(
                30, self.end_date, 31
            )
```

## Review questions
1. Why do we count only receipts arriving during supplier lead time?
2. Why is the fallback labeled non-AI, even though the application uses AI elsewhere?
3. What is the difference between a missing DailySales row and a recorded zero?
4. Why is rounding applied once after the final calculation?
5. How do `status='unavailable'` and `reorder_quantity=None` differ from a valid recommendation of zero?
6. What risks remain if actual deliveries or sales differ from the forecast?

## Limitations
The 15 new tests use mocks, not a new real-product end-to-end reorder probe. The broad AI exception handler can mask different failure causes; the fallback exception handler only catches `ValueError` and `TypeError`. The fallback uses the most recent uninterrupted run ending at the cutoff, at most 59 days. `Product.on_hand` is current even when a historical `end_date` is supplied, so this is not a historical stock backtest. No UI, automation, purchasing action, model training or schema migration was added.
