# Final regression

## Step 549 — Resolve final UI regression failures

### Goal
Check the entire project after UI changes, then isolate and correct any regressions.

```powershell
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test
```
User reported zero system issues, no migration changes, 528/530 tests passed. Both failures expected the original forecast label.

### MODIFIED — NEW presentation correction
**File: `intelligence/templates/intelligence/dashboard.html`**:
```html
<details class="mt-3"><summary class="text-primary">Show daily forecast (30 days)</summary>
```
Restore the tested wording without changing the weekly/daily data or collapse behavior. Source 0adf849. A repeat full run before pulling the fix had the same two failures.

```powershell
git pull --ff-only origin main
git log -1 --oneline
python manage.py test intelligence.test_dashboard.IntelligenceDashboardTests.test_all_results_are_displayed intelligence.test_presentation_rescue.PresentationRescueTests.test_daily_forecast_is_collapsed
```
User confirmed HEAD 0adf849 and 2/2 tests passed in 1.253 seconds. All 530 tests have passed across the full and targeted runs; a single clean post-fix full run is not yet recorded. Outstanding browser/mobile checks and receipt history/detail (not implemented) remain separate. Preserve m8-complete tag. No new feature module.
