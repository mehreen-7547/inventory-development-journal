# Module 4 — Chapter 3: Sales Recording Interface

**Journal Steps 317–321 · Interactive Steps 458–460**  
**Source:** [`8edb896`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/8edb8960b7aa2ec54e6305de6ab6042dee61d2c9) on `main`; user terminal confirmed **59 Sales tests and 170 total tests**, no migrations.

## Before this chapter

The sales service already validated orders, wrote Sale/SaleLine records and deducted stock, but users could not submit a sale through the website. This chapter connects a simple five-row form to that service, creates a confirmation page and adds dashboard links. No voiding or sale-history list was implemented here.

<a id="step-317"></a>
## Step 317 — Build the five-row SaleForm

### Before this step
Sales transaction logic existed but no form converted browser inputs to service-ready items.

### What we did and why
We added five optional product/quantity pairs and discount fields. Only active in-stock products appear in the dropdown.

**Filename: `sales/forms.py`** — verified source excerpt:
```python
for number in range(1, 6):
    self.fields[f'product_{number}'] = forms.ModelChoiceField(
        queryset=products, required=False, label=f'Product {number}'
    )
    self.fields[f'quantity_{number}'] = forms.IntegerField(
        min_value=1, required=False, label='Quantity'
    )
```

### Code walkthrough
The loop creates ten form fields without repeating code. `ModelChoiceField` checks product IDs against the queryset; `IntegerField` checks quantities.

### How we checked it
GitHub source confirms `SaleForm`; 59 Sales tests passed.

### Next
Validate the whole basket.

<a id="step-318"></a>
## Step 318 — Validate items and discounts in the form

### Before this step
Individual fields cannot detect selecting the same product in two rows or a missing product/quantity pair.

### What we did and why
We implemented `clean()` to assemble a list of `{product_id, quantity}` dictionaries, reject duplicates and incomplete rows, and validate discount combinations.

**Filename: `sales/forms.py`** — verified source excerpt:
```python
if product.pk in selected_products:
    raise forms.ValidationError(
        'The same product cannot be selected twice.'
    )

selected_products.add(product.pk)
items.append({'product_id': product.pk, 'quantity': quantity})
```

### Code walkthrough
`clean()` runs after Django validates fields. A set tracks products already chosen. The service repeats critical validation because requests may bypass this form.

### How we checked it
Tests include empty-basket and duplicate-product rejection.

### Next
Connect form submission to the service.

<a id="step-319"></a>
## Step 319 — Add protected create and detail views

### Before this step
Users needed a page to submit sales and another to see the saved result.

### What we did and why
We added `sale_create()` and `sale_detail()` with login and role restrictions. The POST handler calls `record_sale()` and redirects after success; sales staff can see only their own transactions.

**Filename: `sales/views.py`** — verified source excerpt:
```python
@login_required
@role_required('sales', 'manager')
def sale_create(request):
    if request.method == 'POST':
        form = SaleForm(request.POST)
        submission_key = request.POST.get('submission_key', '')
```

### Code walkthrough
A new GET creates a UUID submission key; a failed POST preserves its key for retry. `record_sale()` remains responsible for stock changes and database transactions.

### How we checked it
Source has both views and tests for GET/POST, permissions and duplicate POSTs.

### Next
Expose the URLs.

<a id="step-320"></a>
## Step 320 — Add routes, templates and dashboard links

### Before this step
A view cannot be reached until its URL is registered, and it needs an HTML template.

### What we did and why
We registered `/sales/add/` and `/sales/<int:sale_id>/`, created form and detail templates, and linked Record Sale from manager and sales dashboards.

**Filename: `sales/urls.py`** — verified source excerpt:
```python
urlpatterns = [
    path('sales/add/', views.sale_create, name='sale_create'),
    path('sales/<int:sale_id>/', views.sale_detail, name='sale_detail'),
]
```

### Code walkthrough
The names are used by `redirect()` and `{% url %}`. The form includes `{% csrf_token %}` and a hidden submission key. The detail page displays saved line snapshots and totals.

### How we checked it
GitHub confirms the URLs, both templates and dashboard links.

### Next
Test and publish the chapter.

<a id="step-321"></a>
## Step 321 — Test, clean whitespace and publish

### Before this step
We needed to verify the page workflow and prevent accidental regressions.

### What we did and why
We added page/form tests, ran Django checks and the full suite, fixed trailing whitespace in `sales/tests.py`, staged the seven changed files and pushed the chapter.

**Filename: `sales/tests.py`** — verified source excerpt:
```python
class SalePageTests(TestCase):
    def test_sale_form_requires_product(self):
        form = SaleForm(data={
            'discount_type': 'none',
            'discount_value': '0'
        })
        self.assertFalse(form.is_valid())
```

### Code walkthrough
The final source contains more than eight new test methods: the submitted results rose from 27 to 59 Sales tests. The new page test class reuses fixture setup and also checks access, redirects, duplicates and successful recording. Test counts describe the actual source, not the originally expected estimate.

### How we checked it
User terminal: 59 Sales tests and 170 total tests passed; no migrations. GitHub confirms commit `8edb896`.

### Next
Next chapter: sale history and browsing.

