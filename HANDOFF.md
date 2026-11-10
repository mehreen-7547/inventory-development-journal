# Development handoff — verified through Module 2 Chapter 3

## Repository boundaries
- Application source: `mehreen-7547/ai-assisted-inventory-manager`, branch `main`.
- Beginner study journal: `mehreen-7547/inventory-development-journal`, branch `study-2`. Do not overwrite `main` or `study-3`.

## Latest source checkpoint
- Chapter: Module 2, Chapter 3 — Edit Supplier.
- Last interactive development step: 398 (source commit/push).
- Continuous journal steps: 1–260, 34 chapters; Chapter 3 = 252–260.
- Source commit: [`2b580fbd82ed4285f972c07cb8daf35782ea932c`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/2b580fbd82ed4285f972c07cb8daf35782ea932c).
- Verified files: `catalog/views.py`, `catalog/urls.py`, `catalog/templates/catalog/supplier_list.html`, `catalog/templates/catalog/supplier_form.html`, `catalog/static/catalog/style.css`.

## Completed Chapter 3 behavior
- Manager-only Supplier list/create/edit.
- Single Edit Supplier button switches list to selection mode with `?mode=edit`.
- Clicking supplier name opens prefilled edit form.
- Shared create/edit form uses `form.instance.pk` to switch heading/button and show edit Cancel.
- External Catalog CSS styles the table and selectable rows.
- User reported successful browser checks and `python manage.py check` with no issues.

## Important lessons from troubleshooting
- Do not overwrite `supplier_list.html` with the form template.
- CSS was initially 404; `python manage.py findstatic catalog/style.css --verbosity 2` located it, and after server restart the styled table appeared. Do not assert a proven root cause.
- No Supplier deactivate feature or automated edit test suite was implemented in Chapter 3.

## Next-session rules
- Do not begin Chapter 4 until the study-2 journal is verified remotely.
- Teach exactly one development step at a time, with precise filename, NEW vs CLASS marker, explanation, verification and Next control.
- At chapter completion: consolidate source Git stage/commit/push; verify published source; update study-2 chapter, README, STEP_INDEX, glossary and handoff; verify remote.
- Keep code style aligned with the instructor's Django examples; do not add scope without approval.
