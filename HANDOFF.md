# Development handoff — Module 6 Chapter 6 completed

## Current checkpoint
- Source: https://github.com/mehreen-7547/ai-assisted-inventory-manager, branch `main`, latest commit `a800ee7` (Chapter 6 feature commit `9bc0d92`, follow-up test correction `a800ee7`).
- Journal: https://github.com/mehreen-7547/inventory-development-journal, branch `study-2` ONLY.
- Module 6 Chapter 6 completed: Step 377. All five Manager chapters preserved; Sales Staff dashboard added with own sales totals, recent sales and stock alerts.
- Last complete suite: **336 passed** (616.410 s) after Chapter 5. Chapter 6: **12 new tests**, **51 dashboard tests passed** (58.205 s), full suite not rerun; expected full count 348.
- Chapter 6 changed `core/dashboard.py`, `core/views.py`, `core/templates/core/home.html`, `core/test_sales_staff_dashboard.py` and corrected `core/test_recent_activity_dashboard.py`.
- Local untracked `purchasing.zip` is unrelated and must not be staged.

## Frozen Module 6 scope
- No new apps, database tables, model fields, libraries, chart libraries or dashboard editing. Function-based views, queries in plain functions in `core/dashboard.py`; computed values are not stored.
- Today = `timezone.localdate()`; last 7 days includes today and six previous days; last 30 days includes today and 29 previous days. Completed sales count in totals; voided sales appear only in recent lists. Only active products and suppliers count. Open orders are ordered or partially received; overdue orders are open with expected date before today. Every empty panel needs a one-line message.
- Manager dashboard chapters Ch1–Ch5: DONE (summary cards, stock/PO alerts, recent movements/sales, top sellers and seven-day trend).
- Sales dashboard Ch6: DONE (own today/last-seven-day completed sales, overall out-of-stock count, own eight recent sales, eight stock alerts, no purchasing/supplier/team figures).
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
Module 6 Chapter 7: Administrator Dashboard (account figures, role counts, pending password setup, recent accounts and existing resend setup email link). No business figures.

## Deferred ideas
None proposed.

## Deferred UI and receipt review (user feedback after Chapter 4)
- The goods receipt form at `/purchase-orders/3/receive/` has cramped, misaligned table columns and quantity inputs; fix with responsive Bootstrap layout during UI refinement.
- Add a professional, responsive, role-specific navigation sidebar/drawer and visual consistency pass after Module 6 functionality is finished.
- Review existing goods receipt access; add read-only goods receipt history and details linked from the relevant purchase order if not already available. Treat this as a separate functional gap outside frozen Module 6 scope, not as a new model/table requirement.

## Testing cadence agreement after Chapter 5
- Current suite: 336 tests; last full run 616.410 seconds; completed and pushed at `1e64eb7`.
- For next chapters, run new chapter tests plus relevant existing tests each change; run the full regression suite after every 2–3 chapters, after high-risk auth/stock/transaction changes, and at module completion. Communicate clearly when a commit has **not** had a full-suite run since the last milestone.

## Chapter 6 regression test correction
- First source commit `9bc0d92` passed 48 discovered dashboard tests, but incorrect indentation had hidden three tests. Source commit `a800ee7` restored them; 51 dashboard tests passed.
- After new tests or edits to test classes, compare discovered-test counts, not only `OK`.
- Full suite milestone was 336 tests after Chapter 5; expected count after adding 12 Chapter 6 tests is 348. The full suite has not yet been rerun.
