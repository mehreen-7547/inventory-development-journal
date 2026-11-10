# Module 2 — Chapter 10: Final Test Correction and Module Close

**Journal Step 284 · Interactive Steps 416–417**

<a id="step-284"></a>
## Step 284 — Check the real Opening Stock input and close Module 2

### Before this step
Chapter 9 introduced an Opening Stock box when registering a product. We also made sure that box does not appear when editing a product, because changing a product's name or price must not silently change its stock.

One test was too weak. It searched the edit page for `name="Opening Stock"`. But **Opening Stock** is the label people read; the actual HTML input is named `opening_stock`. The old test could pass without checking the real field.

### What we did and why
We changed the test to search for the actual field name. This is like checking whether the correct door is locked rather than checking whether a sign is on the door.

**Filename: `catalog/tests.py`** — in `ProductOpeningStockTests.test_opening_stock_not_on_edit_form`

Before:
```python
self.assertNotContains(response, 'name="Opening Stock"')
```

After:
```python
self.assertNotContains(response, 'name="opening_stock"')
```

### Code walkthrough
`response` is the HTML page returned when the test visits Edit Product. `assertNotContains` means: *the specified text must not appear on this page*. The lowercase, underscored `opening_stock` is the real name of the form input. The test now checks the exact thing we intended to hide.

No view, form, template, model or database change was needed. This correction makes the automated test more trustworthy; it does not change how the feature works.

### How we checked it
The user ran `python manage.py test`: **87 tests passed** and Django reported no system-check issues. The Git diff showed exactly one line changed in `catalog/tests.py`. The change was committed and pushed to source `main` as [`61c812b`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/61c812b).

### Next
**Module 2 is complete.** Module 3 starts with stock adjustments: correcting a product's recorded quantity after a real shelf count, with a reason and a history entry. We will reuse the existing `change_stock()` service.
