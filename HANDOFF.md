# Development handoff — verified through Module 4 Chapter 2

## Repositories
- Source: `mehreen-7547/ai-assisted-inventory-manager`, branch `main`.
- Journal: `mehreen-7547/inventory-development-journal`, branch `study-2`. Never modify journal `main` or `study-3`.

## Latest checkpoint
- Interactive development Step **457**; journal Step **316**; **45 chapters**.
- Source commit: [`b2ae5c6`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/b2ae5c6355e98e3ad9c0a5206aec2ba3be9a6418), confirmed on GitHub.
- User terminal: **27 Sales tests and 138 total tests passed**; no pending migrations. User subsequently confirmed successful commit/push.
- Journal: [Module 4 Chapter 2](Module-4/02-recording-sales.md).

## Implemented in Chapter 2
- `sales/services.py`: `record_sale(items, user, submission_key, discount_type='none', discount_value=0)`.
- Sales/manager role and active-user validation; active products, positive quantities and duplicate-item validation.
- Fixed and percentage discounts; sale totals and historical SaleLine name/price snapshots.
- `transaction.atomic()` saves Sale, SaleLine and stock movements together; a late failure rolls everything back.
- Existing matching submission key returns the original sale; mismatched request/user is rejected. Database uniqueness and nested savepoint handle insert integrity errors.
- `sales/tests.py`: 27 Sales tests in total, including 3 earlier model tests; 138 full-project tests.

## Not implemented yet
- Sales forms, views, checkout templates, customer-facing sales routes and dashboards.
- `void_sale()` and stock reversal; void metadata fields in the model do not mean voiding works.
- No concurrent multi-process stress test or guarantee that a racing retry returns the existing sale: the loser can receive a controlled error.

## Next work
- Module 4 Chapter 3: implement the user-facing Sales recording workflow in small, tested steps. Reuse `record_sale()` rather than changing stock directly in a view. Verify the frozen plan before deciding exact page design or voiding order.

## Workflow requirements
- Exact filenames and NEW vs CLASS markers; concise explanations; group related code edits.
- At chapter close: run tests, stage/commit/push source; then publish and verify chapter plus README, STEP_INDEX, SOURCE_MAPPING, GLOSSARY and HANDOFF on journal `study-2` before advancing.

## Previous checkpoint
- Module 4 Chapter 1: Sales models, migrations, admin and empty URL skeleton; commits `18022db` and `7af5b5e`, 114 tests.
- Module 3 Chapter 2: stock ledger integrity command; commit `5d662eb`.
