# Module 3 — Chapter 1: Stock Adjustments and Product Action Modes

**Journal Steps 285–290 · Interactive Steps 418–423**  
**Verified source:** [commit `983d4b0`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/983d4b0) on source `main`.  
**Important numbering note:** The interactive development conversation used Steps 418–423, whereas the `study-2` journal has consecutive journal Steps 1–284 through Module 2. We preserve both numbers instead of pretending Steps 285–417 already exist in this journal.

## Chapter introduction — Why this feature exists

A shop's computer may say 25 notebooks are available, but a physical shelf count might find only 23. The manager needs a controlled way to correct that number without rewriting old sales or opening-stock records. Every correction must explain why it happened, record the before-and-after quantities, and identify the employee.

This chapter extends the stock service from Module 2, creates an adjustment form and page, protects against a form opened before another stock change, and makes Edit, History, and Adjust separate selection modes on the Products list. We did not create a new database table or migration.

<a id="step-285"></a>
## Step 285 — Extend the stock service (interactive Steps 418–419)

### Before this step
`inventory/services.py` already had `change_stock()`, which updated `Product.on_hand` and created a `StockMovement` record together. It did not yet offer a dedicated physical-count adjustment with an expected starting balance.

### What we did and why
We added an optional `expected_balance` check to the same database update. A manager's old browser tab should not silently overwrite stock changed by another operation.

**Filename: `inventory/services.py`** — relevant code from `change_stock()`:
```python
with transaction.atomic():
    products = Product.objects.filter(pk=product_id)

    if expected_balance is not None:
        products = products.filter(on_hand=expected_balance)

    if delta < 0:
        products = products.filter(on_hand__gte=-delta)

    changed = products.update(on_hand=F('on_hand') + delta)
```

**Filename: `inventory/services.py`** — the new adjustment helper:
```python
def adjust_stock(
    product_id, counted_quantity, reason, user,
    expected_balance
):
    if type(counted_quantity) is not int or counted_quantity < 0:
        raise ValueError(
            'Counted quantity must be zero or greater'
        )

    if type(expected_balance) is not int or expected_balance < 0:
        raise ValueError('Invalid recorded quantity')

    if not reason or not reason.strip():
        raise ValueError('Reason is required')

    difference = counted_quantity - expected_balance

    if difference == 0:
        raise ValueError(
            'Counted quantity is already correct'
        )

    return change_stock(
        product_id=product_id,
        delta=difference,
        movement_type='adjustment',
        reason=reason.strip(),
        user=user,
        expected_balance=expected_balance
    )
```

### Code walkthrough
`expected_balance` is the quantity shown when the form was opened. The filtered `update()` changes the row only if that number still matches the database. `F('on_hand') + delta` makes the database perform the addition rather than using an old Python value. `on_hand__gte=-delta` prevents a negative result for reductions. If no row was updated, the service distinguishes a missing product, a stale balance, or insufficient stock. `transaction.atomic()` keeps the balance update and movement record in one transaction.

`adjust_stock()` does not directly set the stock to the counted number. It calculates the difference and delegates to `change_stock()`, so the normal history-writing rules still apply. The helper rejects negative counts, missing reasons, and a zero difference.

### How we checked it
The final source contains both functions. The user's browser tests confirmed reductions, increases, zero-difference rejection, and stale-form rejection. The source test class `StockAdjustmentTests` includes these cases.

### Next
Provide an HTML form that captures the physical count, reason, and starting balance.

<a id="step-286"></a>
## Step 286 — Build the adjustment form, view, route and page (interactive Step 419)

### Before this step
The stock service could perform a correction, but the manager had no browser form to submit it.

### What we did and why
We added a Django form, a Manager-only view, a URL and a template. A successful adjustment redirects to that product's history.

**Filename: `inventory/forms.py`**:
```python
from django import forms

class StockAdjustmentForm(forms.Form):
    expected_balance = forms.IntegerField(
        min_value=0,
        widget=forms.HiddenInput
    )
    counted_quantity = forms.IntegerField(
        min_value=0,
        label='Physically Counted Quantity'
    )
    reason = forms.CharField(
        widget=forms.Textarea(attrs={'rows': 3}),
        label='Reason for Adjustment'
    )
```

**Filename: `inventory/views.py`** — essential flow from `stock_adjustment()`:
```python
@login_required
@role_required('manager')
def stock_adjustment(request, product_id):
    product = get_object_or_404(Product, pk=product_id)

    if not product.is_active:
        return redirect('product_list')

    if request.method == 'POST':
        form = StockAdjustmentForm(request.POST)
        if form.is_valid():
            try:
                adjust_stock(
                    product_id=product.pk,
                    counted_quantity=form.cleaned_data['counted_quantity'],
                    reason=form.cleaned_data['reason'],
                    user=request.user,
                    expected_balance=form.cleaned_data['expected_balance']
                )
            except ValueError as error:
                form.add_error(None, str(error))
            else:
                return redirect(
                    'product_stock_history',
                    product_id=product.pk
                )
    else:
        form = StockAdjustmentForm(initial={
            'expected_balance': product.on_hand
        })

    return render(request, 'inventory/stock_adjustment.html', {
        'form': form,
        'product': product
    })
```

**Filename: `inventory/urls.py`**:
```python
from django.urls import path
from . import views

urlpatterns = [
    path(
        'products/<int:product_id>/adjust/',
        views.stock_adjustment,
        name='stock_adjustment'
    ),
]
```

**Filename: `config/urls.py`** — added `path('', include('inventory.urls'))` to connect the app's routes.

**Filename: `inventory/templates/inventory/stock_adjustment.html`** — key form markup:
```html
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit" class="btn btn-primary">
        Save Adjustment
    </button>
    <a href="{% url 'product_list' %}"
       class="btn btn-outline-secondary">Cancel</a>
</form>
```

### Code walkthrough
`forms.Form` validates submitted values without editing a model directly. The hidden `expected_balance` remembers the starting number; it is not treated as proof that the number is still current. The service checks it against the database. `@login_required` requires login, and `@role_required('manager')` restricts the action to managers. The view rejects inactive products, handles GET and POST separately, displays validation errors on the same page, and redirects to history only on success. `{% csrf_token %}` protects the POST form against cross-site request forgery.

### How we checked it
The source contains the route, form, view, and template. The user's browser testing confirmed that saving an adjustment opens Stock History. No migration was required.

### Next
Test the form and its permissions, then improve navigation from the Products list.

<a id="step-287"></a>
## Step 287 — Verify stock corrections in tests and the browser (interactive Steps 419 and 422)

### Before this step
A successful-looking form is not enough. We need evidence that both the balance and history agree, and that invalid requests leave stock unchanged.

### What we did and why
We added ten focused tests for the new adjustment workflow.

**Filename: `inventory/tests.py`** — `StockAdjustmentTests` checks:
```python
def test_manager_can_reduce_stock(self):
    self.client.force_login(self.manager)

    response = self.client.post(self.url, {
        'expected_balance': 25,
        'counted_quantity': 23,
        'reason': 'Two damaged notebooks'
    })

    self.assertRedirects(
        response,
        reverse('product_stock_history', args=[self.product.pk])
    )

    self.product.refresh_from_db()
    self.assertEqual(self.product.on_hand, 23)
```

### Code walkthrough
`force_login()` simulates an authenticated manager. `self.client.post()` submits the form as a browser would. `assertRedirects()` checks the success destination. `refresh_from_db()` reloads the product from the database before checking its quantity. The complete test also checks the movement's delta, before/after balances, reason, and employee. Other tests cover increases, stale data, unchanged counts, invalid values, sales restrictions, anonymous access, and inactive products.

### How we checked it
The user reported all four browser scenarios passed using a notebook: 25 → 23 (history −2), 23 → 26 (history +3), 26 → 26 rejected, and a stale form rejected after a valid change brought the stock to 25. The user later confirmed the automated tests and final browser checks passed. The published commit contains the ten tests; the exact full-suite output was not pasted into the journal conversation.

### Next
Make History and Adjust selectable directly from the product list instead of requiring the Edit Product page.

<a id="step-288"></a>
## Step 288 — Add Edit, History and Adjust selection modes (interactive Step 420)

### Before this step
Managers had to open a product's Edit form to find Stock History or Adjust Stock. That mixed product-detail editing with stock operations.

### What we did and why
We reused the existing `?mode=edit` pattern for `?mode=history` and `?mode=adjust`. The selected mode changes which URL a product name opens.

**Filename: `catalog/views.py`** — revised `product_list()`:
```python
@login_required
@role_required('manager', 'sales')
def product_list(request):
    products = Product.objects.select_related(
        'supplier'
    ).all().order_by('name')

    mode = ''

    if request.user.role == 'manager':
        selected_mode = request.GET.get('mode', '')

        if selected_mode in ['edit', 'history', 'adjust']:
            mode = selected_mode

    return render(request, 'catalog/product_list.html', {
        'products': products,
        'mode': mode,
        'edit_mode': mode == 'edit'
    })
```

**Filename: `catalog/templates/catalog/product_list.html`** — relevant selection logic:
```html
{% if mode == 'edit' %}
    <a class="row-link"
       href="{% url 'product_edit' product.id %}">
        {{ product.name }}
    </a>
{% elif mode == 'history' %}
    <a class="row-link"
       href="{% url 'product_stock_history' product.id %}">
        {{ product.name }}
    </a>
{% elif mode == 'adjust' and product.is_active %}
    <a class="row-link"
       href="{% url 'stock_adjustment' product.id %}">
        {{ product.name }}
    </a>
{% else %}
    {{ product.name }}
{% endif %}
```

### Code walkthrough
The query parameter after `?` selects a viewing mode; it does not change database records. The view accepts only three known values and only for Managers. In the template, the same product name links to Edit, History, or Adjust depending on the mode. Inactive products may still show history but cannot be selected for adjustment. The page has separate mode buttons and a Cancel action.

**Filename: `catalog/tests.py`** — `ProductModeTests` adds eight tests for normal, edit, history, and adjust modes, inactive-product handling, sales restrictions, and unknown modes.

### How we checked it
The user confirmed the new navigation worked in the browser, and all automated tests passed. The final source has 8 new mode tests in addition to the 10 adjustment tests.

### Next
Remove the now-duplicated stock buttons from Edit Product.

<a id="step-289"></a>
## Step 289 — Keep stock actions off the Edit Product form (interactive Step 421)

### Before this step
The product list had dedicated Stock History and Adjust Stock modes, but the Edit Product page still had links to those operations. This duplicated navigation.

### What we did and why
We removed both links from the edit form while retaining the product fields and the active/inactive status form.

**Filename: `catalog/templates/catalog/product_form.html`** — the final form still ends its status section with:
```html
<form method="POST"
      action="{% url 'toggle_product_status' form.instance.pk %}"
      class="mb-4">
    {% csrf_token %}
    {% if form.instance.is_active %}
        <button type="submit"
                class="btn btn-outline-danger">
            Deactivate Product
        </button>
    {% else %}
        <button type="submit"
                class="btn btn-outline-success">
            Reactivate Product
        </button>
    {% endif %}
</form>
```

### Code walkthrough
The edit page remains responsible for product details and activation status. Stock operations live on their own routes. Removing links does not remove their views, URLs, history, or permissions. The user's intermediate template had an empty `if form.instance.is_active` block; this was also removed.

### How we checked it
The final source template has no Stock History or Adjust Stock links. The user confirmed this in the browser.

### Next
Run the final checks and publish all related changes in one source commit.

<a id="step-290"></a>
## Step 290 — Verify and publish Chapter 1 (interactive Steps 422–423)

### Before this step
The chapter had 11 changed files spanning the service, form, view, routes, templates, and tests. They needed one verified source commit.

### What we did and why
The user reported that `python manage.py check`, the migration check, focused tests, the full suite, and final browser checks passed. Git staged exactly the 11 intended files.

**Command context: PowerShell in the Django source repository**:
```powershell
git diff --cached --check
git diff --cached --stat
git diff --cached --name-only
git commit -m "Module 3 Chapter 1: Stock adjustments and product action modes"
git push origin main
git log -1 --oneline
git status --short
```

### Code/command walkthrough
`git diff --cached --check` checks staged text formatting; it reported whitespace warnings in `catalog/tests.py` and `inventory/services.py`. The user proceeded, so those warnings remain in the published commit. `git diff --cached --name-only` listed the 11 staged files. `git commit` saved the snapshot locally; `git push origin main` published it. `git status --short` printed nothing, meaning the local working tree was clean.

### How we checked it
The user's terminal showed `61c812b..983d4b0 main -> main` and `983d4b0 (HEAD -> main, origin/main)`. The GitHub commit was independently fetched and its 11 filenames confirmed. The source implementation was also read back from GitHub.

**Published source:** [`983d4b0` — Stock adjustments and product action modes](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/983d4b0).

### Next
Module 3, Chapter 1 is complete. Continue to the next planned Module 3 chapter only after this journal entry is verified. Do not modify the `study-3` branch.

## Chapter recap

The manager can now choose a product from the Products list in Edit, History, or Adjust mode. A stock adjustment takes a physical count and reason, computes the difference, records a movement, rejects stale or invalid submissions, and opens the product's Stock History after saving. No new model or migration was needed.
