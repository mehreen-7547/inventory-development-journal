# Source mapping — verified project chapters

This map links journal explanations to source files and GitHub commits. It is not a list of planned but unimplemented features.

| Chapter | Source commit | Source files / responsibility | Journal |
|---|---|---|---|
| Module 2 Chapter 8 | [`110c30f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/110c30f) | `inventory/models.py` — `StockMovement`; `inventory/services.py` — `change_stock()`; inventory history views/tests | [Stock movement ledger](Module-2/08-stock-movement-ledger-and-history.md) |
| Module 2 Chapter 9 | [`75fce94`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/75fce94) | Product registration and opening-stock movement | [Opening stock](Module-2/09-opening-stock-registration.md) |
| Module 3 Chapter 1 | [`983d4b0`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/983d4b0) | `inventory/services.py` — `adjust_stock()`; `inventory/forms.py`; `inventory/views.py`; `inventory/urls.py`; `inventory/templates/inventory/stock_adjustment.html`; `catalog/views.py`; product templates; `inventory/tests.py`; `catalog/tests.py`; `config/urls.py` | [Stock adjustments and product action modes](Module-3/01-stock-adjustments-and-product-modes.md) |
| Module 3 Chapter 2 | [`5d662eb`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5d662eb) | `inventory/management/__init__.py` and `inventory/management/commands/__init__.py` — package discovery; `inventory/management/commands/check_ledger.py` — ledger integrity command; `inventory/tests.py` — command and integrity tests | [Stock ledger integrity](Module-3/02-stock-ledger-integrity.md) |

| Module 4 Chapter 1 | [`18022db`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/18022db), [`7af5b5e`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/7af5b5e) | `config/settings.py`, `config/urls.py`, `sales/apps.py`, `sales/models.py`, `sales/admin.py`, `sales/migrations/0001_initial.py`, `sales/tests.py`, `sales/urls.py` — Sales schema and URL setup | [Sales Setup](Module-4/01-sales-setup.md) |

| Module 4 Chapter 2 | [`b2ae5c6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/b2ae5c6355e98e3ad9c0a5206aec2ba3be9a6418) | `sales/services.py` — atomic `record_sale()`, stock movements, discounts and duplicate-submission checks; `sales/tests.py` — transaction, permission, validation and rollback tests | [Recording Sales](Module-4/02-recording-sales.md) |

| Module 4 Chapter 3 | [`8edb896`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/8edb8960b7aa2ec54e6305de6ab6042dee61d2c9) | `sales/forms.py`, `sales/views.py`, `sales/urls.py`, two `sales/templates/sales/` pages, `core/templates/core/home.html`, `sales/tests.py` — sales recording interface | [Sales Recording Interface](Module-4/03-sales-recording-interface.md) |

**Latest source checkpoint:** `8edb896` on `main`; user reported 59 Sales tests and 170 total tests passed, with no pending migrations. Earlier chapters and their exact source mappings are described in their individual journal files; this is a focused map for the current inventory work, not a claim to have remapped every historic source file.

## Module 4 — Chapter 4: Sales History
- Source commit: [`664cf33`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/664cf33), branch `main`.
- Journal: [Steps 322–326](Module-4/04-sales-history.md).
- Changed files: `sales/views.py`, `sales/urls.py`, `sales/tests.py`, `sales/templates/sales/sale_list.html`, `sales/templates/sales/sale_detail.html`, `core/templates/core/home.html`.
- Verified user terminal: 12 history tests, 71 Sales tests, 182 full-project tests passed; whitespace checks passed; source pushed.
- History browsing, search, status filter, pagination and role restrictions implemented. No voiding service or reversal yet.

## Module 4 — Chapter 5: Sale Voiding and Stock Reversal
- Source commit: [`f202c39`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/f202c39), branch `main`.
- Journal: [Steps 327–331](Module-4/05-sale-voiding-and-stock-reversal.md).
- Changed files: `sales/services.py`, `sales/forms.py`, `sales/views.py`, `sales/urls.py`, `sales/tests.py`, `sales/templates/sales/sale_void.html`, `sales/templates/sales/sale_detail.html`.
- Manager-only voiding, positive stock restoration, audit metadata, confirmation form, status display and 14 new tests are in source. User terminal confirmed successful push and clean tree; test output not supplied.

**Final Module 4 verification:** User terminal confirmed 196 tests passed (198.781s), 0 Django system-check issues and no pending migrations after source commit `f202c39`.

## Module 5 — Chapter 1: Purchase Order Setup
- Source commit: [`45bd7a2`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/45bd7a265a4b0eb8a3eac846582dede8d027b42f), branch `main`.
- Journal: [Steps 332–336](Module-5/01-purchase-order-setup.md).
- Changed files: `config/settings.py`, `purchasing/__init__.py`, `purchasing/admin.py`, `purchasing/apps.py`, `purchasing/migrations/0001_initial.py`, `purchasing/migrations/__init__.py`, `purchasing/models.py`, `purchasing/tests.py`, `purchasing/views.py`.
- User reported all Chapter 1 checks passing; remote source commit verified. Detailed test terminal output not provided.

## Module 5 — Chapter 2: Purchase Order Creation Service
- Source commit: [`5160b69`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5160b691a8dafbc509f4b478c765fb003a2874a7), branch `main`.
- Journal: [Steps 337–342](Module-5/02-purchase-order-creation-service.md).
- Changed files: `purchasing/services.py` (new), `purchasing/tests.py` (expanded).
- User terminal: 17 focused service tests reported passed; 221 full-project tests passed (209.321s), zero system-check issues, no pending migrations, successful push and clean working tree.

## Module 5 — Chapter 5: Goods Receipt and Close Short
- Verified source commit: [`788a158`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/788a1580c1d7a8a9c6fb264eec605e50a2efcd6a).
- Journal: [Steps 361–371](Module-5/06-goods-receipt-and-close-short.md).
- Files: `purchasing/forms.py`, `purchasing/migrations/0002_purchaseorderline_quantity_received_and_more.py`, `purchasing/models.py`, `purchasing/services.py`, `purchasing/templates/purchasing/goods_receipt_form.html`, `purchasing/templates/purchasing/purchase_order_detail.html`, `purchasing/test_close_short.py`, `purchasing/test_goods_receipt.py`, `purchasing/test_goods_receipt_views.py`, `purchasing/urls.py`, `purchasing/views.py`.
- Verification: 87 purchasing tests passed; full-project terminal output not supplied.

## Module 7 — Chapter 2: Historical CSV Import
- Source: [`adb1355`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/adb1355) on `main` (compared to `21d51cf`).
- Journal: [Steps 392–405](Module-7/02-historical-csv-import.md).
- New: `intelligence/csv_validation.py`, `intelligence/test_csv_validation.py`.
- Modified: `intelligence/services.py`, `inventory/test_seed_integration.py`.
- Behavior: strict CSV parsing and SKU checks; atomic upsert into DailySales; no physical stock change; date-dependent seed test fixed.
- User terminal: 18 focused CSV tests passed, isolated seed correction 1/1 passed, full regression 421/421 passed (729.967s). Source pushed successfully. Whitespace warnings remained in committed source.
- Unrelated untracked local files `purchasing.zip` and `reset_business_data_once.py` excluded.

## Module 7 — Chapter 3: Historical Data Reconciliation
- Source: [`ff0c68d`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/ff0c68d) on `main` (compared with `adb1355`, one commit ahead).
- Journal: [Steps 407–419](Module-7/03-historical-data-reconciliation.md).
- New: `intelligence/migrations/0002_dailysales_source.py`.
- Modified: `intelligence/models.py`, `intelligence/services.py`, `intelligence/test_csv_validation.py`, `intelligence/test_daily_sales.py`.
- Behavior: DailySales source recorded/imported; synchronization takes priority in its date range, including zero-sales days; CSV skips recorded rows and updates imported rows.
- Verified GitHub diff: five files, 172 insertions and 7 deletions. User terminal: 427/427 full tests passed (617.512s); 29/29 focused tests passed after whitespace cleanup (4.473s); Django check clean. Source push confirmed.
- Limitations: existing rows default recorded; no independently verified historical provenance or coverage calendar. Import count includes skipped rows. Concurrency race not separately tested.

## Module 7 — Chapter 4: Forecasting Data Preparation
- Source: [`65ca45f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/65ca45f), verified against `ff0c68d`: one commit, two added files, 130 additions, zero deletions.
- Journal: [Steps 420–424](Module-7/04-forecasting-data-preparation.md).
- NEW: `intelligence/forecasting_data.py` (read-only, gap-checked 60-day units sequence) and `intelligence/test_forecasting_data.py` (six tests).
- Verification: Django check 0 issues; no migrations; 35/35 focused tests passed (4.593s); user reported full regression OK (full count not shown); push confirmed.
- Limit: no AI inference, forecasts, MAE or UI yet; unrelated untracked files excluded.

## Module 7 — Chapter 5: Chronos-Bolt Tiny Demand Forecasting
- Source: [`2c93e25`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/2c93e25), verified against `65ca45f`: one commit, two NEW files, 204 additions (85 + 119), zero deletions.
- Journal: [Steps 425–429](Module-7/05-chronos-bolt-tiny-integration.md), branch `study-2`.
- NEW: `intelligence/forecasting.py` (cached pretrained CPU model; 60-day history → 30 dated P10/P50/P90 estimates, validation and nonnegative clipping); `intelligence/test_forecasting.py` (five mocked inference-service tests).
- Verification: 11/11 focused tests passed, Django check zero issues, no migrations; real `scripts/ai_probe.py --model bolt` on synthetic 90-day history: WORKED YES, 34.99 seconds total, 339.6 MB peak memory, 0.01 seconds forecast computation; full regression ended OK (count not supplied); source push confirmed.
- Limitations: probe is synthetic and separate from the new service; no end-to-end real product inference or forecast accuracy/MAE verified; no UI, stock writes or automatic ordering. Local `ai_probe_results.json` not staged; unrelated untracked files untouched.

## Module 7 — Chapter 6: Historical Forecast Accuracy Evaluation
- Source: [`b3b1051`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/b3b1051), verified against `2c93e25`: one commit, two NEW files, 226 additions (96 + 130), zero deletions.
- Journal: [Steps 430–435](Module-7/06-forecast-accuracy-evaluation.md), branch `study-2`.
- NEW `intelligence/forecast_evaluation.py`: `calculate_mae` validates and scores predictions; `evaluate_product_forecast` separates 90 days into 60 context / 30 held-out actuals, requests Chronos P50 forecasts at cutoff, compares to a fixed seven-day moving-average baseline and returns both MAEs and lower-error method. Read-only.
- NEW `intelligence/test_forecast_evaluation.py`: eight mocked/deterministic tests for arithmetic, invalid inputs, Chronos win, baseline win, tie, wrong length, wrong dates, and missing history.
- User verification: 19 focused tests passed, zero Django issues, no migrations; full regression `OK` (count not supplied), successful push. Local evaluation ending 2026-11-13 succeeded on five eligible products (IDs 30–34); Chronos lower MAE on four, moving average on one. Exact per-product values are recorded in Chapter 6.
- Limits: single 30-day holdout per product, local historical/demo dataset, no statistical superiority claim, no UI/stock writes, no evaluation result persisted. Untracked `purchasing.zip` and `reset_business_data_once.py` untouched.
