# Module 7 — Chapter 7: Stock-Shortage Prediction (Steps 436–450)

## Chapter introduction — why this feature exists
The previous chapter produced 30-day demand forecasts, but a forecast alone does not answer the inventory manager's operational question: **when might a product fall below its safety stock or run out?** This chapter combines today's recorded on-hand quantity, the pretrained model's **P50** daily demand estimate, and outstanding purchase-order quantities with expected delivery dates. It produces a **read-only 30-day projection** and the first predicted safety-stock breach and stockout dates. It does not order stock, change ledger balances, train a model, or claim certainty about actual future sales or deliveries.

**Verified source:** [`c226ceb`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/c226ceb) on `main`, parent `b3b1051`: three files changed, 372 insertions, eight deletions. **NEW** `intelligence/shortage_prediction.py` (123 lines), **NEW** `intelligence/test_shortage_prediction.py` (232 lines), **EXISTING** `inventory/test_seed_integration.py` (date-stability fix). User reported 11/11 shortage tests and 457/457 full tests passing; Django check, syntax compilation and Git whitespace check passed.

### Data flow
`Product.on_hand` + `Product.safety_stock` + `forecast_product_demand(product_id, end_date)` → validate 30 consecutive dated P50 predictions → collect remaining quantities on outstanding purchase-order lines → separate scheduled, unscheduled and overdue arrivals → add arrivals and subtract forecast demand each day → first projected safety-stock breach / first projected stockout + daily table.

**Terminology:** `end_date` is the forecast cutoff. The first projected day is `end_date + 1`. `on_hand` is the product's **current database balance**, not a reconstructed historical balance for an old cutoff. Therefore a historical cutoff used with current stock is a functional integration probe, **not a historical stock backtest**.

## Step 436 — Decide how undated incoming stock is handled

### Before this step
A purchase order can be outstanding without a known `expected_delivery_date`. Adding it on an invented date would make the projection look more certain than the available data.

### What we did
**DESIGN DECISION — no source file yet.** Outstanding quantities with `expected_delivery_date=None` are **not** added to any projected day. They are returned separately as `unscheduled_incoming`. We count only the **remaining** amount, not the original ordered amount when part has already been received.

### Code — `intelligence/shortage_prediction.py` (implemented in Step 437)
```python
        if delivery_date is None:
            unscheduled_incoming += remaining
            continue
```

### Code explanation
The `continue` prevents an undated order from entering `incoming_by_date`. The quantity remains visible for staff review but does not falsely postpone a predicted shortage.

### How we checked it
The user approved this handling as option **A**. A deterministic test later asserts `unscheduled_incoming == 25` and zero scheduled arrivals on day one.

### Next
Implement the projection service against the actual product, forecasting and purchase-order models.

## Step 437 — Implement the read-only shortage service

### Before this step
The existing `forecast_product_demand` returned 30 dated P10/P50/P90 estimates. `Product` already stored `on_hand`, `safety_stock` and `lead_time_days`; purchasing stored ordered and received quantities and an optional expected delivery date. We needed a service to connect them without writing inventory transactions.

### What we did
**NEW FILE — `intelligence/shortage_prediction.py`**, function `predict_stock_shortage(product_id, end_date)`. It reads the product, calls the existing pretrained forecast service, queries outstanding purchase-order lines and constructs 30 projected stock balances.

**Code — `intelligence/shortage_prediction.py` (dependencies and entry point):**
```python
from datetime import timedelta
from math import isfinite

from django.db.models import F

from catalog.models import Product
from purchasing.models import PurchaseOrderLine

from .forecasting import forecast_product_demand


def predict_stock_shortage(product_id, end_date):

    product = Product.objects.get(id=product_id)

    forecast = forecast_product_demand(
        product_id,
        end_date
    )

    predictions = forecast['predictions']
```

### Code explanation
- `Product.objects.get` retrieves actual recorded on-hand and safety stock. It raises the usual Django `DoesNotExist` error for an invalid product ID; this service does not turn it into an HTTP response.
- `forecast_product_demand` is the Chapter 5 read-only model integration. We reuse its predictions instead of loading a second model.
- `F` lets the database compare ordered and received quantities on the same purchase-order line.
- The service currently uses **P50**, not P90; it represents a central-demand scenario, not a worst-case risk bound.

**Code — `intelligence/shortage_prediction.py` (eligible incoming orders):**
```python
    incoming_lines = PurchaseOrderLine.objects.filter(
        product_id=product_id,
        purchase_order__status__in=[
            'ordered',
            'partially_received'
        ],
        quantity_ordered__gt=F('quantity_received')
    ).select_related('purchase_order')

    incoming_by_date = {}
    unscheduled_incoming = 0
    overdue_incoming = 0

    for line in incoming_lines:
        remaining = (
            line.quantity_ordered - line.quantity_received
        )

        delivery_date = (
            line.purchase_order.expected_delivery_date
        )

        if delivery_date is None:
            unscheduled_incoming += remaining
            continue

        if delivery_date <= end_date:
            overdue_incoming += remaining
            continue

        incoming_by_date[delivery_date] = (
            incoming_by_date.get(delivery_date, 0)
            + remaining
        )
```

### Code explanation
- The status filter includes only `ordered` and `partially_received` orders; drafts, fully received, cancelled and closed-short orders are not future arrivals.
- `quantity_ordered__gt=F('quantity_received')` ignores lines with no outstanding units. For eligible lines, `remaining` subtracts receipts already booked.
- `select_related('purchase_order')` loads the parent order alongside its line to avoid an additional query per line.
- `incoming_by_date` sums multiple deliveries scheduled for the same date. Undated and overdue quantities are **reported**, but are not silently assumed to arrive within the projection.
- A scheduled arrival after the 30-day horizon remains in the date dictionary but does not affect the returned 30 days.

**Code — `intelligence/shortage_prediction.py` (daily projection):**
```python
    projected_stock = float(product.on_hand)

    first_safety_breach = None
    first_stockout = None

    daily_projection = []

    for prediction in predictions:
        date = prediction['date']
        demand = prediction['p50']

        incoming = incoming_by_date.get(date, 0)

        projected_stock += incoming
        projected_stock -= demand

        if (
            first_safety_breach is None
            and projected_stock < product.safety_stock
        ):
            first_safety_breach = date

        if (
            first_stockout is None
            and projected_stock < 0
        ):
            first_stockout = date

        daily_projection.append({
            'date': date,
            'predicted_demand': demand,
            'incoming': incoming,
            'projected_stock': round(projected_stock, 2)
        })
```

### Code explanation
- Start from the current `on_hand`. On each forecast date, add scheduled incoming units and subtract that day's predicted P50 sales.
- `first_safety_breach` is the first date projected stock is **strictly below** safety stock. `first_stockout` is the first date projected stock is **strictly below zero**. These are different events: safety stock is an early warning, while a negative projection indicates unmet demand.
- The service **does not clamp negative projected balances to zero**: a negative number shows the size of projected unmet demand, rather than claiming physical stock can be negative.
- The two dates are set once, so later recovery after a delivery does not erase the first warning.
- Rounding is for the displayed daily result; calculations continue with the unrounded running balance.

**Code — `intelligence/shortage_prediction.py` (return structure):**
```python
    return {
        'product_id': product.id,
        'product_name': product.name,
        'on_hand': product.on_hand,
        'safety_stock': product.safety_stock,
        'lead_time_days': product.lead_time_days,
        'unscheduled_incoming': unscheduled_incoming,
        'overdue_incoming': overdue_incoming,
        'first_safety_breach': first_safety_breach,
        'first_stockout': first_stockout,
        'daily_projection': daily_projection
    }
```

### Code explanation
The result includes identifying information, stock context, unscheduled and overdue incoming quantities, first alert dates, and 30 per-day records. `lead_time_days` is included for downstream planning but **does not yet affect this prediction calculation**. This chapter does not implement reorder quantity recommendations.

### How we checked it
The committed service was fetched from GitHub at `c226ceb` and compared with the approved scope: no model changes, migrations, database writes, order placement or user interface.

### Next
Create deterministic tests for arrivals, stock warnings and partial receipts.

## Step 438 — Add the first six shortage tests and correct one expected date

### Before this step
A forecast service may be expensive to run and can vary with real historical inputs. We needed fast tests of the stock arithmetic independent of pretrained-model inference and the database.

### What we did
**NEW FILE — `intelligence/test_shortage_prediction.py`; CLASS — `StockShortagePredictionTests(SimpleTestCase)`.** The class uses `SimpleNamespace` for a product and purchase-order lines, and `unittest.mock.patch` for the product lookup, forecasting call and purchase-order query.

**Code — `intelligence/test_shortage_prediction.py` (fixture and forecast builder):**
```python
class StockShortagePredictionTests(SimpleTestCase):

    def setUp(self):
        self.end_date = date(2026, 11, 13)
        self.product = SimpleNamespace(
            id=30,
            name='Test Notebook',
            on_hand=20,
            safety_stock=5,
            lead_time_days=7
        )

    def make_forecast(self, demand=5):
        return {
            'predictions': [
                {
                    'date': self.end_date + timedelta(days=day),
                    'p50': demand
                }
                for day in range(1, 31)
            ]
        }
```

### Code explanation
The fixed date makes each forecast date reproducible. The default starting stock is 20 units and daily demand is five units; a safety-stock threshold of five distinguishes a warning from a stockout. The forecast builder creates exactly 30 sequential dates.

**Code — `intelligence/test_shortage_prediction.py` (mock service call):**
```python
    def run_prediction(self, lines=None, demand=5):
        with patch(
            'intelligence.shortage_prediction.Product.objects.get'
        ) as get_product, patch(
            'intelligence.shortage_prediction.forecast_product_demand'
        ) as forecast, patch(
            'intelligence.shortage_prediction.PurchaseOrderLine.objects.filter'
        ) as filter_lines:

            get_product.return_value = self.product
            forecast.return_value = self.make_forecast(demand)

            filter_lines.return_value.select_related.return_value = (
                lines if lines is not None else []
            )

            return predict_stock_shortage(30, self.end_date)
```

### Code explanation
The patches replace collaborators **at their import location inside the service**. The test still executes the real projection logic, but avoids database setup and model inference. `select_related.return_value` supplies the iterable that the service expects.

**Code — `intelligence/test_shortage_prediction.py` (no incoming stock):**
```python
    def test_shortage_without_incoming_stock(self):
        result = self.run_prediction()

        self.assertEqual(
            result['first_safety_breach'],
            date(2026, 11, 17)
        )
        self.assertEqual(
            result['first_stockout'],
            date(2026, 11, 18)
        )
```

### Code explanation
From 20 units, subtracting five per day gives 15, 10, 5, 0, then -5. Safety stock is **breached** on the fourth day (0 < 5); stockout is flagged on the fifth (-5 < 0). Equality to safety stock and equality to zero do not trigger the respective strict-below tests.

**Code — `intelligence/test_shortage_prediction.py` (scheduled receipt):**
```python
    def test_dated_incoming_prevents_early_shortage(self):
        delivery = self.end_date + timedelta(days=3)

        line = self.make_line(
            'ordered', delivery, 30
        )

        result = self.run_prediction([line])

        self.assertEqual(
            result['daily_projection'][2]['incoming'],
            30
        )
        self.assertEqual(
            result['first_safety_breach'],
            date(2026, 11, 23)
        )
```

### Code explanation
The receipt of 30 on day three postpones the first breach but does not prevent it forever. An initial test expectation incorrectly assumed no later breach; it was corrected to **November 23** after tracing the full 30-day projection. Tests then passed **6/6**.

### How we checked it
The six original cases cover no incoming stock, dated incoming stock, partial receipt, undated incoming, same-day multiple deliveries and zero demand. User terminal reported **six passing tests** after the correction. The committed file includes these cases plus the five added next.

### Next
Reject invalid model outputs and distinguish overdue orders from genuinely scheduled arrivals.

## Step 439 — Validate forecasts and exclude overdue arrivals

### Before this step
The service initially trusted the forecast's length, date alignment and numeric values. It also needed an explicit rule for orders dated on or before the cutoff: those units are not guaranteed to have arrived.

### What we did
**UPDATED — `intelligence/shortage_prediction.py`**, with validation before purchase-order processing and an `overdue_incoming` counter. **UPDATED — `intelligence/test_shortage_prediction.py`**, five new tests (11 total).

**Code — `intelligence/shortage_prediction.py` (forecast contract):**
```python
    if len(predictions) != 30:
        raise ValueError('Expected 30 forecast days')

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
```

### Code explanation
- The service expects **exactly 30** predictions, in strict daily order starting the day after the cutoff. This prevents mixing demand with the wrong dates.
- Each P50 must be a finite, nonnegative Python `int` or `float`. `bool` is rejected explicitly because Python otherwise treats booleans as integers. `NaN`, infinity and negative demand are rejected.
- Validation runs **before** the outstanding-order query and projection. Bad forecasts fail visibly rather than producing plausible-looking stock alerts.

**Code — `intelligence/shortage_prediction.py` (overdue rule):**
```python
        if delivery_date <= end_date:
            overdue_incoming += remaining
            continue
```

### Code explanation
An order due before or **on** the cutoff is overdue for this forecast calculation. Its remaining units are reported but not added to any future day; otherwise the system would pretend an unconfirmed receipt already happened.

**Code — `intelligence/test_shortage_prediction.py` (selected regression checks):**
```python
    def test_delivery_on_cutoff_date_is_overdue(self):
        line = self.make_line(
            'ordered', self.end_date, 10
        )

        result = self.run_prediction([line], demand=0)

        self.assertEqual(result['overdue_incoming'], 10)
        self.assertEqual(
            result['daily_projection'][0]['incoming'],
            0
        )
```

```python
    def test_rejects_invalid_forecast_demand(self):
        for invalid in [-1, float('nan'), float('inf'), True]:
            forecast = self.make_forecast()
            forecast['predictions'][0]['p50'] = invalid

            with self.subTest(value=invalid):
                with patch(
                    'intelligence.shortage_prediction.Product.objects.get',
                    return_value=self.product
                ), patch(
                    'intelligence.shortage_prediction.forecast_product_demand',
                    return_value=forecast
                ):
                    with self.assertRaisesRegex(
                        ValueError, 'Invalid forecast demand'
                    ):
                        predict_stock_shortage(
                            30, self.end_date
                        )
```

### Code explanation
The first test proves an on-cutoff delivery is excluded. The second uses `subTest` to check four invalid numeric cases without repeating test functions. Other new tests cover older overdue deliveries, wrong forecast length and wrong forecast dates.

### How we checked it
User terminal: **11/11 shortage tests passed**. These are deterministic mocked tests, not real Chronos accuracy tests.

### Next
Call the service with real existing product and order records.

## Step 440 — Run real database and pretrained-model integration

### Before this step
Mocks established correct arithmetic and validation, but did not prove the service could read actual Django models and call Chronos successfully in the current environment.

### What we did
**No source file changed.** The user ran `predict_stock_shortage` for product IDs **30–34** with forecast cutoff **2026-11-13**, using the local database and real pretrained Chronos service.

**Code — terminal integration invocation (not a committed file):**
```python
from datetime import date
from intelligence.shortage_prediction import predict_stock_shortage

end_date = date(2026, 11, 13)
for product_id in [30, 31, 32, 33, 34]:
    result = predict_stock_shortage(product_id, end_date)
    print(product_id, result['on_hand'],
          result['daily_projection'][-1]['projected_stock'],
          result['first_safety_breach'], result['first_stockout'])
```

### Code explanation
This illustrates the service calls made after configuring Django in the local script. The service looks up each product, obtains its 30-day forecast, reads outstanding purchase orders and returns projected stock. It does **not** create purchase orders or update `Product.on_hand`.

### How we checked it — user-reported local output

| Product ID | Current on-hand | Projected day-30 stock | First safety breach | First stockout |
|---|---:|---:|---|---|
| 30 | 82 | 75.37 | None | None |
| 31 | 119 | 105.69 | None | None |
| 32 | 120 | 116.13 | None | None |
| 33 | 109 | 108.44 | None | None |
| 34 | 46 | 44.12 | None | None |

All five calls completed. Reported unscheduled and overdue incoming were zero for these products. **This proves the service runs on those records, not that stockouts cannot occur**. Because the cutoff is historical but on-hand is current, these numbers must not be presented as a historical stock backtest or as validated future outcomes.

### Next
Run the full regression and investigate any failures before committing.

## Step 441 — Discover the date-dependent seed integration failure

### Before this step
The new shortage tests passed, but the entire project suite could reveal an interaction with existing seed, inventory or dashboard functionality.

### What we did
**EXISTING TEST — `inventory/test_seed_integration.py`.** The first full run found **457 tests**, with one failure after about **656 seconds**: `SeedDemoDataIntegrationTests.test_purchase_orders_and_goods_receipts` expected 32 purchase orders, but received 31. The source commit was **stopped**.

A temporary diagnostic test, **`inventory/test_seed_order_diagnostic.py`** (not committed), called the seed command in an isolated test database. It reported 31 purchase orders, 29 goods receipts, 28 received orders, two ordered and one partially received on **2026-11-14**.

**Code — `inventory/management/commands/seed_demo_data.py` (existing, inspected only):**
```python
        first_day = timezone.localdate() - timedelta(days=365)
        last_day = first_day + timedelta(days=364)
        rng = random.Random(2026)
```

### Code explanation
Although the random generator uses a fixed seed, the **start date moves each day**. The seed logic varies sales volumes using `day.weekday()`, and replenishment depends on inventory and sales. Changing the calendar alignment changes the generated sequence of purchase orders. Thus a hard-coded expected count is not stable if the test uses the live system date.

### How we checked it
The failure was reproduced by a focused test. The temporary diagnostic printed the counts and confirmed the date dependency; it was later removed. No source code was committed during the failed run.

### Next
Inspect the working tree and find a reproducible reference date before changing any expected assertions.

## Step 442 — Inspect the seed generator and correct the working directory

### Before this step
The user was asked to inspect date and replenishment references in `seed_demo_data.py`, but the initial PowerShell command ran from a parent directory and failed to locate the file or Git repository.

### What we did
**No source change.** The user switched into the actual Django project root and reran `Select-String` and `git status --short`. The output confirmed:
- `timezone.localdate()` determines the first date.
- `random.Random(2026)` is fixed.
- `day.weekday() >= 5` changes weekend demand.
- `regular_restock`, `shortage_risk` and `create_open_orders` control replenishment.

### How we checked it
The successful file search printed matching source lines and Git status. Git status also showed an unexpected modification in `intelligence/test_daily_sales.py`.

### Next
Inspect that unrelated modification before modifying tests.

## Step 443 — Review the unexpected daily-sales test diff

### Before this step
We must not accidentally stage a file unrelated to shortage prediction.

### What we did
**EXISTING — `intelligence/test_daily_sales.py`** was inspected using:
```powershell
git diff -- intelligence/test_daily_sales.py
git status --short
```

The diff showed **only two added blank lines at the end** of the file, with no test logic change.

### How we checked it
The user supplied the complete diff showing the trailing blank lines. The file was identified as safe to restore without losing functional edits.

### Next
Remove the whitespace-only change and the first temporary diagnostic.

## Step 444 — Clean up unrelated changes

### Before this step
The working tree contained the intended shortage files, an unexpected whitespace-only edit, and a temporary diagnostic test.

### What we did
**RESTORED — `intelligence/test_daily_sales.py`** and **REMOVED TEMPORARY — `inventory/test_seed_order_diagnostic.py`**:
```powershell
git restore -- intelligence/test_daily_sales.py
Remove-Item "inventory/test_seed_order_diagnostic.py"
git status --short
```

### How we checked it
Git status then listed only the two new shortage files and the unrelated untracked `purchasing.zip` and `reset_business_data_once.py`. No intended source change was lost.

### Next
Find a fixed date that produces the test's originally expected seed counts.

## Step 445 — Verify a stable seed reference date

### Before this step
Changing expected 32 orders to 31 would only make the test pass for one calendar day. We needed to keep meaningful assertions while controlling the date.

### What we did
**NEW TEMPORARY — `inventory/test_seed_date_diagnostic.py`** (later removed). The diagnostic patched the seed command's `timezone.localdate` to **2026-11-13**, ran it in Django's isolated test database, and counted orders and receipts.

**Code — `inventory/test_seed_date_diagnostic.py` (essential pattern, temporary):**
```python
        reference_date = date(2026, 11, 13)

        with patch(
            'inventory.management.commands.seed_demo_data.timezone.localdate',
            return_value=reference_date
        ):
            call_command(
                'seed_demo_data',
                manager=manager.username,
                stdout=StringIO()
            )
```

### Code explanation
`patch` replaces the date function during the seed command only, so the production seed command remains untouched. Django `TestCase` provides an isolated test database. The reference date is selected because it reproduces the established deterministic fixture, not because the system clock should be changed.

### How we checked it
User terminal: **one diagnostic test passed**, in **16.237 seconds**. With November 13 as the reference, the command created **32 purchase orders**, **30 goods receipts** and **29 received orders**, matching the original integration expectations.

### Next
Freeze the integration test's seed date and rerun its seven tests.

## Step 446 — Stabilize the seed integration test

### Before this step
The seed test still used `timezone.localdate()` and therefore produced different sales/PO patterns when the real date changed.

### What we did
**EXISTING FILE — `inventory/test_seed_integration.py`; CLASS — `SeedDemoDataIntegrationTests(TestCase)`; METHOD — `setUpTestData`.** Imported `date` and `patch`, set a fixed `cls.today`, and patched the seed command only while it populated the test database.

**Code — `inventory/test_seed_integration.py` (committed setup):**
```python
        cls.today = date(2026, 11, 13)
        cls.first_day = cls.today - timedelta(days=365)
        cls.last_day = cls.today - timedelta(days=1)

        cls.seed_output = StringIO()

        with patch(
            'inventory.management.commands.seed_demo_data.timezone.localdate',
            return_value=cls.today
        ):
            call_command(
                'seed_demo_data',
                manager=cls.manager.username,
                stdout=cls.seed_output
            )
```

### Code explanation
All seed-derived expected dates now follow the same fixed reference. The test retains the original **32/30/29** count assertions instead of weakening them. The patch does not alter the seed command in production.

### How we checked it
The first rerun reached a **different** failure: the dashboard's seven-day series ended on the real **2026-11-14**, while `self.today` was the fixed **2026-11-13**. This showed that dashboard date calls also needed isolation. The next step corrects this separate test-time dependency.

### Next
Make the dashboard test use the same fixed date without changing dashboard production behavior.

## Step 447 — Fix the dashboard test's reference date

### Before this step
The seed was fixed to November 13, but dashboard functions still called the real `timezone.localdate()`. The expected final chart date was November 13, while the actual result was November 14. One attempted scripted replacement did not apply; source inspection confirmed the original method remained.

### What we did
**EXISTING FILE — `inventory/test_seed_integration.py`; CLASS — `SeedDemoDataIntegrationTests`; METHOD — `test_dashboards_use_generated_history`.** Added a test-only decorator that patches the dashboard's date function for the duration of this method.

**Code — `inventory/test_seed_integration.py` (committed decorator):**
```python
    @patch(
        'core.dashboard.timezone.localdate',
        return_value=date(2026, 11, 13)
    )
    def test_dashboards_use_generated_history(self, mock_localdate):
        manager_summary = get_manager_summary()
```

### Code explanation
- The decorator makes all dashboard functions called in this test agree on the same fixed reference day as the seed data.
- The mock argument is required by the decorator; the test does not need to inspect it.
- The patch is confined to this test method. Production dashboards still use the actual local date.

### How we checked it
User terminal: **7/7 integration tests passed** in **7.291 seconds**; `git diff --check` reported no errors. The source file was later verified in the pushed commit.

### Next
Remove the second temporary diagnostic and rerun the complete project suite.

## Step 448 — Final regression verification

### Before this step
The targeted integration failure was fixed, but the project-wide suite had to pass before any source commit. The temporary date diagnostic also had to be removed.

### What we did
**REMOVED TEMPORARY — `inventory/test_seed_date_diagnostic.py`**. Ran the shortage tests, all Django tests, `manage.py check`, Python syntax compilation of the three intended files, and `git diff --check`. No migrations or database schema changes were made.

**Code — PowerShell (verification commands):**
```powershell
Remove-Item "inventory/test_seed_date_diagnostic.py"
python manage.py test intelligence.test_shortage_prediction -v 1
python manage.py test -v 1
python manage.py check
python -m py_compile intelligence/shortage_prediction.py intelligence/test_shortage_prediction.py inventory/test_seed_integration.py
git diff --check
git status --short
```

### How we checked it
User terminal:
```text
Ran 11 tests in 0.015s
OK
Ran 457 tests in 557.941s
OK
System check identified no issues (0 silenced).
```
The Python compilation and whitespace checks completed without errors. Git status showed only the three intended files plus the two unrelated untracked local files.

### Next
Stage exactly the three approved source files, commit and push.

## Step 449 — Commit and push verified Chapter 7 source

### Before this step
All focused and full tests passed. We needed an auditable source commit that excluded temporary diagnostics and unrelated local files.

### What we did
**NEW — `intelligence/shortage_prediction.py`; NEW — `intelligence/test_shortage_prediction.py`; MODIFIED — `inventory/test_seed_integration.py`.** The user explicitly staged these three paths, verified the staged list, committed and pushed to `main`.

**Code — PowerShell (essential Git commands):**
```powershell
git add -- intelligence/shortage_prediction.py intelligence/test_shortage_prediction.py inventory/test_seed_integration.py
git diff --cached --check
git commit -m "Add stock shortage prediction and stabilize seed integration tests"
git push origin main
git log -1 --oneline
git status --short
```

### How we checked it
User terminal and GitHub commit independently confirm:
```text
[main c226ceb] Add stock shortage prediction and stabilize seed integration tests
 3 files changed, 372 insertions(+), 8 deletions(-)
 create mode 100644 intelligence/shortage_prediction.py
 create mode 100644 intelligence/test_shortage_prediction.py
 b3b1051..c226ceb  main -> main
c226ceb (HEAD -> main, origin/main)
?? purchasing.zip
?? reset_business_data_once.py
```
The two unrelated untracked files were not staged or pushed.

### Next
Publish the corresponding detailed journal chapter and update navigation, mapping, glossary and handoff on `study-2`.

## Step 450 — Publish and verify the Chapter 7 journal

### Before this step
The source code was already committed and verified, but the separate study journal needed to preserve the decisions, actual code, debugging sequence, testing evidence and next implementation boundary.

### What we did
**NEW JOURNAL — `Module-7/07-stock-shortage-prediction.md`**; **UPDATED JOURNAL — `README.md`, `STEP_INDEX.md`, `SOURCE_MAPPING.md`, `GLOSSARY.md`, `HANDOFF.md`** on branch `study-2`. The source repository stays on `main` at `c226ceb`; the journal publication does not change source code.

### How we checked it
Source commit `c226ceb` and its three-file diff were fetched independently from GitHub before publication. Journal files are fetched again from `study-2` after publication to verify the chapter and cross-references.

### Next
Continue with the next approved intelligence workflow. A likely next feature is a **reorder recommendation** using projected shortages and supplier lead time, but first confirm the agreed formula, business rules and academic scope. Do not automatically place orders or assume an approved recommendation formula.

---

## Chapter review — explain it independently
1. **Why use P50?** It is the existing model's central demand estimate, not a guaranteed outcome.
2. **Why add scheduled receipts before subtracting that day's demand?** This chapter assumes stock due on a date is available for that day's projected demand; actual receipt timing may differ.
3. **Why exclude undated and overdue orders?** Their arrival cannot be confidently assigned to a future date.
4. **Why subtract received quantities from ordered quantities?** Only units still outstanding could arrive later.
5. **What does a safety breach mean?** Projected stock is strictly below `safety_stock`; a stockout requires projected stock below zero.
6. **Is `lead_time_days` used to calculate the shortage?** No. It is returned for later planning.
7. **Did we retrain Chronos or change inventory?** No. The service is read-only and reuses pretrained inference.
8. **Why did the seed integration test fail on November 14?** Its fixed expected order count depended on a moving weekday-aligned seed date; the test now uses a reproducible reference date.
9. **Does the real-product probe prove prediction accuracy?** No. It confirms successful integration for five local products, not real future stock outcomes.
