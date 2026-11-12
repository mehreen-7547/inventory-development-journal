# Development handoff — Module 6 Chapter 1 completed

## Current checkpoint
- Source: https://github.com/mehreen-7547/ai-assisted-inventory-manager, branch `main`, commit `81cde18`.
- Journal: https://github.com/mehreen-7547/inventory-development-journal, branch `study-2` ONLY.
- Module 6 Chapter 2 completed: Step 373, six Manager summary cards plus low/out-of-stock panel.
- Full suite: **308 passed**, zero Django system-check issues, no pending migrations.
- Chapter 2 changed four source files: `core/dashboard.py`, `core/views.py`, `core/templates/core/home.html`, `core/test_low_stock_dashboard.py`.
- Local untracked `purchasing.zip` is unrelated and must not be staged.

## Frozen Module 6 scope
- No new apps, database tables, model fields, libraries, chart libraries or dashboard editing. Function-based views, queries in plain functions in `core/dashboard.py`; computed values are not stored.
- Today = `timezone.localdate()`; last 7 days includes today and six previous days; last 30 days includes today and 29 previous days. Completed sales count in totals; voided sales appear only in recent lists. Only active products and suppliers count. Open orders are ordered or partially received; overdue orders are open with expected date before today. Every empty panel needs a one-line message.
- Manager dashboard chapters: Ch1 summary cards DONE; Ch2 low/out-of-stock panel DONE; Ch3 purchase-order attention panel; Ch4 recent stock activity and recent sales; Ch5 top sellers and seven-day daily sales table using Bootstrap progress bars.
- Sales dashboard Ch6: own sales only, three cards, recent own sales, low/out-of-stock read-only list; no other staff revenue, suppliers, purchasing or movements.
- Admin dashboard Ch7: accounts only, four cards, pending setup and recently added accounts; no business metrics.
- Ch8 local reproducible synthetic seed command: fixed random seed, 365 daily sales days ending yesterday, three suppliers, about 25 products and five category strings, opening stock, restocking through existing services, open/overdue orders and ~6 printed anomaly days. Do not create users; require existing Manager; refuse when any Sale exists; use `change_stock`, `record_sale`, `receive_goods`; run `check_ledger`.
- Ch9 seed tests, ledger verification and full tests; Ch10 URL authorization sweep, full tests and `m6-complete` tag. About 92 steps in the frozen plan, but journal numbering follows actual completed steps.
- Known deviation: `select_for_update()` remains in the receipt service; SQLite ignores it. Leave unchanged.

## Chapter-end procedure
- One small step at a time unless user requests rapid mode; each chapter is fully tested and pushed before the next.
- User pushes source with explicit staging and guarded PowerShell block. Assistant verifies and publishes the journal to `study-2` (never `main`).
- Journal includes chapter introduction, precise filenames before each code block, code from the actual source, explanation, tests, previous/next links, glossary when needed, and recall question.
- If a useful feature is outside frozen scope, list it under Deferred ideas and do not implement.

## Next
Module 6 Chapter 3: Manager purchase orders needing attention panel. Preserve existing dashboard cards, stock panel and quick actions.

## Deferred ideas
None proposed.
