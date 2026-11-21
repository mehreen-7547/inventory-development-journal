# UI refinement — Chapter 3: Goods receipt layout

## Step 543 — Make the goods receipt form readable

**Goal:** Fix the crowded, almost unstyled goods-receipt form observed during the seven-stage demonstration, while keeping receipt submission and validation unchanged.

**File: MODIFIED** `purchasing/templates/purchasing/goods_receipt_form.html`

**Source excerpt:**
```django
<form method="post" class="card receipt-form">
    {% csrf_token %}
    <input type="hidden" name="submission_key" value="{{ submission_key }}">
    <div class="table-responsive">
        <table class="table align-middle receipt-table">
```

**Explanation:** Existing POST method, CSRF protection, idempotency submission key, `receipt_rows` iteration, Django form fields, field errors and form-level errors are preserved. The page now uses a concise heading with order/supplier/status, a scrollable Bootstrap table, aligned quantity columns and consistent primary/cancel actions. The template changes presentation only.

**File: MODIFIED** `core/static/core/ui.css`

**Source excerpt:**
```css
.receipt-table { min-width: 750px; }
.receipt-product { min-width: 220px; font-weight: 550; }
.receipt-input input { display: block; width: 100%; min-width: 95px; max-width: 125px; }
.receipt-actions { display: flex; justify-content: flex-end; gap: .6rem; }
```

**Explanation:** A minimum table width prevents column headings from running together; on narrow screens the table scrolls horizontally rather than compressing all six columns. Inputs have a consistent width, and actions align on the right. Styles are centralized rather than repeated inline.

**Why this differs from class:** This is an additional presentation-polish pass, not a change to the instructor-taught Django form submission workflow.

**Source commits:** `0e6ede2`, `d40f25a`.

**Verification:** Files were written to GitHub `main`. Local template rendering, actual goods-receipt screen and regression tests **remain pending**. Check an eligible Ordered or Partially Received PO; do not submit any receipt during visual review.

**Remaining:** Read-only receipt history/detail functionality was previously discussed but is **not implemented** by this step.
