# Development handoff — verified through Module 4 Chapter 1

## Repositories
- Source: `mehreen-7547/ai-assisted-inventory-manager`, branch `main`.
- Journal: `mehreen-7547/inventory-development-journal`, branch `study-2`. Never modify `main` or `study-3` in journal repository.

## Verified checkpoint
- Last interactive development step: **444**.
- Last journal step: **306**; **44 chapters**.
- Source commits: `18022db` (Sales Setup), `7af5b5e` (missing URL integration).
- User terminal: **114 Django tests passed** after both commits; final source branch `main` at `7af5b5e`; clean status output.
- Journal: [Module 4 Chapter 1](Module-4/01-sales-setup.md), journal Steps 295–306, interactive Steps 428–444.

## Implemented in Sales
- `sales` app registered in `config/settings.py`.
- `Sale` and `SaleLine` schema, with protected FKs, discounts and totals fields, submission key, historical snapshots and void metadata.
- Initial migration `sales/migrations/0001_initial.py`.
- Both models registered in Django Admin.
- Three Sales model-structure tests in `sales/tests.py`.
- `config/urls.py` includes `sales.urls`; `sales/urls.py` has an empty `urlpatterns` list.

## Not implemented yet
- No sale creation service, stock deduction, discount calculations, idempotency enforcement, void service, user-facing Sales forms/pages, or sales dashboards. Model fields alone are not working features.
- The approved next area is Sales transaction logic and its tests; follow the frozen v3 plan.

## Migration repair
- An early temporary `Sale`/`SaleItem` migration was applied, then the models were corrected to `Sale`/`SaleLine`. Adding non-null `created_by` triggered a Django default prompt. In the empty development Sales database only, the user dropped Sales tables, cleared Sales migration history, deleted the old initial migration and regenerated a correct `0001_initial.py`. Do **not** repeat that reset against real transaction data.

## Source publication detail
- First commit `18022db` omitted `config/urls.py` and `sales/urls.py` from staging. Second commit `7af5b5e` added both, and 114 tests passed again.

## Workflow requirements
- The user prefers grouped related code changes and terminal commands, concise explanations, exact filenames and NEW vs CLASS markers.
- At each chapter close, run guarded tests, stage, commit and push source; then write and verify the journal chapter and README, STEP_INDEX, GLOSSARY, SOURCE_MAPPING and HANDOFF on `study-2` before advancing.
- Never claim features exist merely because their model fields have been created.

## Navigation
- [README](README.md) · [Step index](STEP_INDEX.md) · [Source mapping](SOURCE_MAPPING.md) · [Glossary](GLOSSARY.md)

## Prior handoff
## Implemented behavior
- Product and supplier management; opening stock and StockMovement history.
- Manager-only stock adjustment using `adjust_stock()`, with expected-balance checks and a product-list action mode.
- Read-only `python manage.py check_ledger` command. It checks movement continuity, arithmetic and final balance against `Product.on_hand`. It reports errors and raises `CommandError` without changing data.
- Four command tests plus two separate movement-integrity tests are present in `inventory/tests.py`.

