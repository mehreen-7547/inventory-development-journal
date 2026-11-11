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
