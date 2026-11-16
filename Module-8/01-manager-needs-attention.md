# Module 8 — Chapter 1: Needs attention today (Steps 508–510)

## Chapter introduction
After Module 7, a manager could inspect one product at a time on Demand Intelligence, but had no combined prioritized view on the main dashboard. The frozen Chapter 1 requirement is to reuse the existing shortage, reorder and anomaly calculations for active products; display at most ten ranked products, with links to their individual intelligence pages; and prevent one product's calculation failure from breaking the entire panel. No new app, table or library is permitted.

**Source commit:** [54bcb7d](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/54bcb7d). **User verification:** Django system check passed, an 83-test run passed, and four focused attention-panel tests passed. No post-change full-project regression result was supplied.

## Step 508 — Calculate a combined attention list

**Before this step.** The Manager homepage displayed operational summaries, while reorder, shortage and anomalies were accessed on the separate intelligence page. We needed a concise cross-product view without duplicating forecasting formulas.

**File: NEW** `core/attention.py`. Function: `get_needs_attention_today()`.

**Code (relevant excerpts):**
```python
for product in Product.objects.filter(is_active=True).order_by('sku'):
    # Each calculation is attempted independently.
    try:
        reorder = recommend_reorder_quantity(product.pk, end_date)
    except Exception:
        had_failure = True
    try:
        shortage = predict_stock_shortage(product.pk, end_date)
    except Exception:
        had_failure = True
    try:
        anomalies = detect_sales_anomalies(product.pk, end_date, days=7)
    except Exception:
        had_failure = True
```

**Walkthrough.** `is_active=True` excludes deactivated products. The service uses each product's most recent sales date up to today as its analysis cutoff, or today's date when none exists. It calls existing services rather than reimplementing their calculations. Independent `try/except` blocks let one unavailable analysis leave other information usable; the panel counts products with any failed calculation. It also calls `get_recent_average()` to calculate days of cover from a complete 28-day history, where available.

**File: NEW** `core/attention.py` (ranking and limit):
```python
if (reorder or {}).get('status') == 'available' and quantity is not None and quantity > 0:
    # Priority 0: reorder; earlier shortage first, then larger order.
    ...
elif average is not None and average > 0 and product.on_hand / average > 90:
    # Priority 1: overstocked, higher cover first.
    ...
elif (anomalies or {}).get('anomaly_count', 0) > 0:
    # Priority 2: unusual sales in the last 7 days.
    ...
items.sort(key=lambda item: item['rank'])
return {'items': items[:10], 'failed_products': failed_products}
```

**Walkthrough.** The `if/elif` chain produces **at most one row per product**: reorder takes precedence, then overstock, then recent anomalies. Rank tuples determine order within each group. `items[:10]` truncates only after sorting, so it displays the ten highest-ranked items across active products. Missing averages are not treated as zero demand. The function produces read-only advice; it never changes stock or creates purchase orders.

**File: MODIFIED** `core/views.py` (Manager branch of `home`):
```python
from .attention import get_needs_attention_today
# Inside the manager-only branch:
context['needs_attention'] = get_needs_attention_today()
```

**Walkthrough.** The existing role-protected `home` view adds the panel data only for managers. Admin and sales users do not trigger this analysis.

**File: MODIFIED** `core/templates/core/home.html` (Manager section):
```django
<h3 class="h5 mb-0">Needs attention today</h3>
{% for item in needs_attention.items %}
<tr>
  <td>{{ item.product.name }}</td>
  <td>{{ item.kind }}</td>
  <td>{{ item.detail }}</td>
  <td><a href="{% url 'intelligence_dashboard' %}?product={{ item.product.pk }}">Demand Intelligence</a></td>
</tr>
{% endfor %}
```

**Walkthrough.** The template shows product, category of attention, a quantity/cover/anomaly explanation, and a link selecting that product on the existing Demand Intelligence page. It also shows an empty-state message or a notice when some calculations were unavailable.

**How we checked it.** The user ran the Chapter 1 patch. Django system check reported no issues; an 83-test run passed. The working tree showed exactly two modified and one new implementation files before focused tests were added.

**Next:** Step 509.

## Step 509 — Add focused dashboard tests

**Before this step.** Passing existing tests did not demonstrate the new ranking, link count, or failure handling. We added specific tests without invoking the real forecasting model.

**File: NEW** `core/test_attention.py`.

**Code (relevant excerpt):**
```python
@patch('core.attention.get_recent_average', return_value=1)
@patch('core.attention.detect_sales_anomalies')
@patch('core.attention.predict_stock_shortage')
@patch('core.attention.recommend_reorder_quantity')
def test_ranked_top_ten_and_links(self, reorder, shortage, anomalies, average):
    ...
    rows = response.context['needs_attention']['items']
    self.assertEqual(len(rows), 10)
    self.assertTrue(all(row['kind'] == 'Reorder now' for row in rows))
    self.assertContains(response, '?product=', count=10)
```

**Walkthrough.** `patch` substitutes deterministic results for the expensive forecasting-related functions. The test creates 12 products and checks that the dashboard shows only ten ranked recommendations with ten product-specific links. Other tests verify that calculation errors do not crash the panel, sales users do not run manager-only analysis, and an empty result displays an understandable message.

**How we checked it.** User ran the verification script: `python manage.py check` reported no issues; `python manage.py test core.test_attention` found four tests and all four passed in 3.093 seconds. The test file appeared as a new untracked file ready to stage.

**Next:** Step 510.

## Step 510 — Commit and push the completed Chapter 1 source

**Before this step.** Implementation and focused tests passed, but they existed only in the local working tree.

**Files: MODIFIED** `core/views.py`, `core/templates/core/home.html`. **Files: NEW** `core/attention.py`, `core/test_attention.py`.

**Code (Git commands):**
```powershell
git add core/attention.py core/test_attention.py core/views.py core/templates/core/home.html
git diff --cached --check
git commit -m "Add manager needs attention dashboard panel"
git push origin main
```

**Walkthrough.** Explicit file paths ensure unrelated local files are not staged. `git diff --cached --check` validates staged whitespace; the commit records the four-file change; `git push origin main` publishes it.

**How we checked it.** The user's terminal confirmed commit `54bcb7d`, **four files changed, 155 insertions**, and successful push `a5ad9ba..54bcb7d main -> main`. The LF-to-CRLF warning for the test file was informational. Unrelated `intelligence.zip` and `reset_business_data_once.py` were not included.

**Next:** Wait for the user's Chapter 1 approval before Module 8 Chapter 2. The frozen next chapter evaluates AI vs seven-day average vs predict-zero over rolling weekly totals for the last eight weeks, across all products.

## Differences from class

**NEW vs CLASS — Separate dashboard calculation module**
- **Where:** `core/attention.py`, `get_needs_attention_today()`
- **In class:** Dashboard calculations were generally written directly in view functions.
- **In this project:** The calculation is a plain helper function imported by the existing view.
- **How they differ:** The view handles requests and rendering; the helper assembles and ranks recommendations.
- **Why chosen:** Clearer view and independent tests.
- **Other option:** Keep all logic inside `home()`.

**NEW vs CLASS — Mocked dashboard tests**
- **Where:** `core/test_attention.py`
- **In class:** Most functionality was demonstrated by manually opening pages.
- **In this project:** Django `TestCase` and `unittest.mock.patch` verify deterministic outcomes.
- **How they differ:** Mocks avoid loading the real forecasting model during focused tests.
- **Why chosen:** Faster, repeatable tests of ranking, permissions, links and error isolation.
- **Other option:** Browser-only verification (less reliable).

## Chapter status
Source code is committed and pushed. Automated tests passed as reported above. **Chapter approval and a live browser review have not yet been supplied.** Do not begin Chapter 2 without the user's OK.
