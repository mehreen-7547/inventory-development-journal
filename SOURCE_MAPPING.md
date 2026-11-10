# Source mapping — verified project chapters

This map links journal explanations to source files and GitHub commits. It is not a list of planned but unimplemented features.

| Chapter | Source commit | Source files / responsibility | Journal |
|---|---|---|---|
| Module 2 Chapter 8 | [`110c30f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/110c30f) | `inventory/models.py` — `StockMovement`; `inventory/services.py` — `change_stock()`; inventory history views/tests | [Stock movement ledger](Module-2/08-stock-movement-ledger-and-history.md) |
| Module 2 Chapter 9 | [`75fce94`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/75fce94) | Product registration and opening-stock movement | [Opening stock](Module-2/09-opening-stock-registration.md) |
| Module 3 Chapter 1 | [`983d4b0`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/983d4b0) | `inventory/services.py` — `adjust_stock()`; `inventory/forms.py`; `inventory/views.py`; `inventory/urls.py`; `inventory/templates/inventory/stock_adjustment.html`; `catalog/views.py`; product templates; `inventory/tests.py`; `catalog/tests.py`; `config/urls.py` | [Stock adjustments and product action modes](Module-3/01-stock-adjustments-and-product-modes.md) |
| Module 3 Chapter 2 | [`5d662eb`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5d662eb) | `inventory/management/__init__.py` and `inventory/management/commands/__init__.py` — package discovery; `inventory/management/commands/check_ledger.py` — ledger integrity command; `inventory/tests.py` — command and integrity tests | [Stock ledger integrity](Module-3/02-stock-ledger-integrity.md) |

| Module 4 Chapter 1 | [`18022db`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/18022db), [`7af5b5e`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/7af5b5e) | `config/settings.py`, `config/urls.py`, `sales/apps.py`, `sales/models.py`, `sales/admin.py`, `sales/migrations/0001_initial.py`, `sales/tests.py`, `sales/urls.py` — Sales schema and URL setup | [Sales Setup](Module-4/01-sales-setup.md) |

**Latest source checkpoint:** `7af5b5e` on `main`; user's terminal showed 114 tests passed and clean working tree. Earlier chapters and their exact source mappings are described in their individual journal files; this is a focused map for the current inventory work, not a claim to have remapped every historic source file.
