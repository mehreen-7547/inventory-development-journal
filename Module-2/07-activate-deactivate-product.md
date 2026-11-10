# MODULE 2 — Supplier and inventory foundations

## Chapter 7: Activate and Deactivate Product (Journal Step 274)

### Before this chapter
Chapter 6 allowed Managers to edit product details. The Product model already had an `is_active` field and the product list displayed Active/Inactive, but Managers could not change product status from the application.

### What we built
A separate status form on the **Edit Product** page, using the same interaction as Supplier Management. A Manager can deactivate and reactivate a product without deleting its record or altering its stock. **Interactive Step 408 corresponds to journal Step 274.**

**Verification evidence:** The user reported 21 passing catalogue tests and seven passing browser checks. These results were run locally by the user, not independently executed here. Source commit: [`ff55d4f`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/ff55d4f), pushed to `main` by the user. The preceding source baseline was `796ccb5`.

<a id="step-274"></a>
### Step 274: Add product activation and deactivation

**Before this step:** A Manager could edit product details, but the Active/Inactive badge was only informational. A status button on the product list would also conflict with the existing Supplier and Employee editing workflow.

**What we did and why:** The status action is a Manager-only POST request. `get_object_or_404` loads the chosen Product or returns 404. The Boolean is flipped and `save(update_fields=['is_active'])` writes only that column. The user returns to the product list, where the badge reflects the new state. This is a soft deactivation, not deletion.

**Filename: `catalog/views.py`**
```python
@login_required
@role_required('manager')
@require_POST
def toggle_product_status(request, product_id):

    product = get_object_or_404(
        Product,
        id=product_id
    )

    product.is_active = not product.is_active
    product.save(update_fields=['is_active'])

    return redirect('product_list')
```

**Filename: `catalog/urls.py`**
```python
path(
    'products/<int:product_id>/status/',
    views.toggle_product_status,
    name='toggle_product_status'
),
```

**Filename: `catalog/templates/catalog/product_form.html`**

The status form appears only for a saved product (`form.instance.pk`). It is placed **after** the product-details form closes, so the HTML forms are not nested. CSRF protection is required for the POST.

```html
{% if form.instance.pk %}
<hr>
<h5 class="mt-4">Product Status</h5>
<p>
    Current status:
    {% if form.instance.is_active %}
        <span class="badge bg-success">Active</span>
    {% else %}
        <span class="badge bg-secondary">Inactive</span>
    {% endif %}
</p>
<form method="POST"
      action="{% url 'toggle_product_status' form.instance.pk %}"
      class="mb-4">
    {% csrf_token %}
    {% if form.instance.is_active %}
        <button type="submit" class="btn btn-outline-danger">
            Deactivate Product
        </button>
    {% else %}
        <button type="submit" class="btn btn-outline-success">
            Reactivate Product
        </button>
    {% endif %}
</form>
{% endif %}
```

**Filename: `catalog/tests.py`**

Six new methods were added inside `ProductListTests`:
- `test_manager_can_deactivate_product` — POST turns Active off, stock remains unchanged and the view redirects.
- `test_manager_can_reactivate_product` — POST turns an inactive product on again.
- `test_get_cannot_change_product_status` — GET returns HTTP 405 without changing status.
- `test_sales_cannot_change_product_status` — Sales Staff cannot perform the change.
- `test_anonymous_cannot_change_product_status` — an unauthenticated request is redirected.
- `test_deactivated_product_remains_in_database` — deactivation preserves the Product row.

Representative test code, **filename: `catalog/tests.py`**:
```python
def test_manager_can_deactivate_product(self):
    self.client.force_login(self.manager)
    response = self.client.post(
        reverse('toggle_product_status', args=[self.product.id])
    )
    self.product.refresh_from_db()
    self.assertFalse(self.product.is_active)
    self.assertEqual(self.product.on_hand, 0)
    self.assertRedirects(response, reverse('product_list'))
```

**How we checked it:** The user confirmed `python manage.py check`, all **21 catalogue tests** and **seven browser checks** passed. The browser checks covered the Manager edit/status workflow, both transitions, unchanged product details/stock and Sales Staff visibility without modification permission. The user subsequently confirmed source commit `ff55d4f` was pushed successfully to `origin/main`. The working tree still contained five unrelated untracked files from a terminal paste issue, requiring separate inspection.

**Next:** Chapter 8 should begin only after the Chapter 7 source commit and this journal update have both been verified.
