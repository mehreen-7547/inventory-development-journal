# Sales and purchase orders

## Step 546 — Refine transaction pages and role-specific search

Keep transaction workflows intact while reducing clutter and ensuring sales staff search only by sale number.

### MODIFIED — CLASS queryset filtering; NEW role-specific search
**File: `sales/views.py`**: the existing staff queryset is already restricted to their own sales. Manager search matches sale number or creator username using Q objects; staff search matches sale_number__icontains only. This is a search refinement, not a new authorization mechanism.

**File: `sales/templates/sales/sale_list.html`**: manager-only Recorded By column and employee search placeholder; staff placeholder Sale number; conditional empty-table colspan.

### MODIFIED — NEW presentation
**Files:** `sales/templates/sales/sale_form.html`, `sales/templates/sales/sale_void.html`, `sales/templates/sales/sale_detail.html`, `purchasing/templates/purchasing/purchase_order_form.html`, `purchasing/templates/purchasing/purchase_order_list.html`, `purchasing/templates/purchasing/purchase_order_detail.html`.
Compact headings, cards and line rows; preserve dynamic add/remove controls, supplier filtering, totals, void permissions and purchase order actions. Cancel on sale form returns to sales list.

**File: `sales/templates/sales/sale_form.html`**: retain the exact tested sentence `Only active products with available stock are shown.` once in the heading, rather than repeating it per line.

Verification: 185/186 sales/purchasing tests initially passed; the wording regression was corrected in 58a7e4e and 14/14 availability tests passed. Source commits 09c6c82, 97bb26a, 1e02ec8, 09676fa, 88c3b0c, 0af4054, aacc259, eb717d9, 58a7e4e. Dedicated browser walkthrough not confirmed.
