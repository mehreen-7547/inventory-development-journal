# Intelligence layout

## Step 547 — Refine intelligence and forecast comparison

### Goal
Make forecast results easier to scan without changing calculations or data.

### MODIFIED — NEW presentation
**File: `intelligence/templates/intelligence/dashboard.html`**: compact product selector and cards, remove redundant bottom dashboard link. Weekly and daily predictions remain in a collapsed details section.

**File: `intelligence/templates/intelligence/weekly_evaluation.html`**: three summary metric tiles, top navigation to Demand Intelligence, scrollable weekly results with sticky table heading. The scroll wrapper limits vertical space without discarding rows. Existing evaluation context and table data remain unchanged.

Source commits e87914c, e06fdb5. The changed forecast summary wording caused two tests to fail; restored in Step 549. Dedicated visual verification not confirmed.
