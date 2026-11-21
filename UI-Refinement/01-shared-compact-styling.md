# UI refinement — Chapter 1: Shared compact styling (Step 541)

## Goal
Make the existing Django interface more compact and consistent without changing business logic. The source milestone `m8-complete` remains unchanged; UI refinement is a new post-milestone phase.

## Step 541 — Introduce a shared UI foundation

**Why:** The app had Bootstrap loaded in the shared base template but no central UI stylesheet. Pages used different spacing and presentation conventions, and some forms and tables looked unstyled.

**File: NEW (not class)** `core/static/core/ui.css`

**Source excerpt:**
```css
:root {
    --ui-bg: #f7f8fa;
    --ui-surface: #fff;
    --ui-text: #243041;
    --ui-border: #e3e7ed;
    --ui-accent: #315a83;
    --ui-radius: 9px;
}
.card { background: var(--ui-surface); border: 1px solid var(--ui-border); border-radius: var(--ui-radius); box-shadow: none !important; }
.card-body { padding: 1rem; }
```

**Explanation:** CSS custom properties centralize the palette and border radius. A single shared stylesheet reduces repeated per-template styling. Compact card padding, typography, form controls, buttons and tables establish a common appearance. Responsive rules reduce spacing further on smaller screens.

**File: MODIFIED (existing/class structure)** `templates/base.html`

**Source excerpt:**
```django
<link href="{% static 'core/ui.css' %}" rel="stylesheet">
{% block extra_css %}{% endblock %}
```

**Explanation:** The global stylesheet is loaded after Bootstrap and before any page-specific CSS, allowing shared defaults while retaining local overrides. The main container now uses the `app-main` class to control width and spacing.

**Verification:** Re-fetched both published files from GitHub `main`; confirmed the stylesheet exists and `base.html` references it. **Local browser verification and Django regression tests are pending**. No views, URLs, models or migrations changed.

**Source commits:** `ab5667c` (new CSS), `27046f6` (base template).

**Next:** Pull `main` locally, review dashboard/products/purchasing visually, and run checks before continuing with the role-specific sidebar and page-level redesign.
