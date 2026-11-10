# MODULE 2 — Supplier and inventory foundations

## Chapter 9: Record Opening Stock When Registering a Product

**Journal Steps 280–283 · Interactive Steps 414–415**

### Before this chapter

Imagine a small stationery shop registering a notebook in our inventory system. The shop already has 25 notebooks on its shelf. In Chapter 8, we built a way to record stock changes and show their history. But the **Add Product** page did not yet ask how many notebooks the shop already had. New products therefore started with zero stock, even when the shop had items available.

In this chapter, we connect the Add Product page to the stock-recording system. **Opening stock** simply means the number of items the shop has when it first registers a product.

**Real example:** Register 25 notebooks. The product's current stock becomes 25, and its history records a change of **+25**, from **0 to 25**.

**Source code:** [Commit `75fce94`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/75fce94) on `main`. The user confirmed the browser workflow and ran all **87 tests successfully**. No database migration was needed.

<a id="step-280"></a>
### Step 280 — Ask for opening stock on the Add Product form

**Before this step:** The product form collected a name, price, supplier and other details. It did not ask for the quantity already on the shelf.

**What we did and why:** We added a field called `opening_stock`. This is a question on the form, not a new column in the Product database table. The existing `on_hand` column already stores current stock. We also hide the new question when editing an existing product: editing its name or price must not accidentally change its stock.

**Filename: `catalog/forms.py`**
```python
class ProductForm(forms.ModelForm):
    opening_stock = forms.IntegerField(
        min_value=0,
        required=True,
        label='Opening Stock',
        help_text='Initial quantity available when registering the product.'
    )
```

**Code walkthrough:** `forms.IntegerField` makes a box that accepts whole numbers. `min_value=0` rejects negative stock; you cannot start with minus five notebooks. `required=True` means the Manager must fill it in, even when the answer is zero. `label` and `help_text` tell the person using the page what to enter.

**Filename: `catalog/forms.py`**
```python
    class Meta:
        model = Product
        fields = [
            'sku', 'name', 'category', 'supplier',
            'unit_price', 'lead_time_days', 'safety_stock'
        ]

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        suppliers = Supplier.objects.filter(is_active=True)

        if self.instance.pk:
            suppliers = (
                Supplier.objects.filter(is_active=True)
                | Supplier.objects.filter(pk=self.instance.supplier_id)
            )
            self.fields.pop('opening_stock')

        self.fields['supplier'].queryset = (
            suppliers.distinct().order_by('name')
        )
```

**Code walkthrough:** `Meta.fields` lists the actual Product details saved by the form. The extra opening-stock question is not in that list because it needs special handling. `__init__` runs when Django prepares the form. `self.instance.pk` means this product already has a database ID, so we are editing rather than creating it. `pop('opening_stock')` removes that question from the edit form. The existing supplier filtering remains: active suppliers can be chosen, and an existing product can retain its current supplier even if that supplier later becomes inactive.

**How we checked it:** The browser showed Opening Stock when adding a product. The seven Chapter 9 tests also covered whether the edit form omits it.

**Next:** Make the entered quantity actually update stock and create a history record.

<a id="step-281"></a>
### Step 281 — Save the new product and its first stock movement together

**Before this step:** The form could collect opening stock, but `form.save()` by itself would only save Product fields. It would not record the opening quantity or its history.

**What we did and why:** We connected the Create Product page to `change_stock()`, the function built in Chapter 8. Think of it as the single approved stock register: it changes the quantity and writes down what happened.

**Filename: `catalog/views.py`**
```python
from django.db import transaction
from inventory.services import change_stock
```

**Code walkthrough:** The first import gives us a way to make related database operations succeed together. The second imports the existing stock-changing function; we are reusing it, not building a second stock system.

**Filename: `catalog/views.py`**
```python
@login_required
@role_required('manager')
def product_create(request):
    if request.method == 'POST':
        form = ProductForm(request.POST)

        if form.is_valid():
            opening_stock = form.cleaned_data['opening_stock']

            with transaction.atomic():
                product = form.save()

                if opening_stock > 0:
                    change_stock(
                        product_id=product.pk,
                        delta=opening_stock,
                        movement_type='opening',
                        reason='Initial stock at product registration',
                        user=request.user
                    )

            return redirect('product_list')
    else:
        form = ProductForm()

    return render(request, 'catalog/product_form.html', {
        'form': form
    })
```

**Code walkthrough:** `@login_required` means the visitor must be signed in; `@role_required('manager')` means only a Manager can use this page. A `POST` is the form being submitted. `form.is_valid()` checks whether the answers follow the rules. `cleaned_data['opening_stock']` retrieves the checked quantity.

`form.save()` first creates the product, initially with zero stock. When the entered quantity is greater than zero, `change_stock()` adds that quantity and writes the opening entry into `StockMovement`. `product.pk` is the new product's database ID; `request.user` identifies the Manager who registered it. If opening stock is zero, we do not create a movement, because nothing changed.

**Why `transaction.atomic()` matters:** Imagine saving the notebook's details works, but writing its opening-stock history fails. We would have an incomplete registration. The `with transaction.atomic():` block treats both saves as one job: if either fails, neither change remains in the database. This is called a **transaction**.

**How we checked it:** The browser test confirmed a newly registered product displayed its opening quantity and a matching entry on the Stock History page.

**Next:** Test ordinary and unusual situations automatically.

<a id="step-282"></a>
### Step 282 — Test opening stock and correct a misleading test

**Before this step:** A page can look correct during one manual test and still fail when someone enters zero, a negative number, or uses the wrong account. We needed repeatable checks.

**What we did and why:** We added seven tests. A **test** is a small program that performs an action and checks the result automatically. Each test starts with its own test data, so it does not modify the shop's actual records.

**Filename: `catalog/tests.py`**
```python
class ProductOpeningStockTests(TestCase):
    def setUp(self):
        self.manager = CustomUser.objects.create_user(
            username='openingmanager',
            email='openingmanager@example.com',
            password='ManagerPass123!',
            role='manager'
        )
        self.sales = CustomUser.objects.create_user(
            username='openingsales',
            email='openingsales@example.com',
            password='SalesPass123!',
            role='sales'
        )
        self.supplier = Supplier.objects.create(
            name='Opening Stock Supplier'
        )
        self.url = reverse('product_create')
        self.data = {
            'sku': 'OPEN001',
            'name': 'Opening Product',
            'category': 'Stationery',
            'supplier': self.supplier.pk,
            'unit_price': '50.00',
            'lead_time_days': 3,
            'safety_stock': 5,
            'opening_stock': 25
        }
```

**Code walkthrough:** `TestCase` is Django's testing helper. `setUp()` prepares a Manager, a Sales employee, a supplier and a sample product form before each test. `reverse('product_create')` finds the correct URL by its Django name, so we do not have to type the web address ourselves. `self.data` is a sample completed form.

**Filename: `catalog/tests.py`**
```python
def test_create_product_with_opening_stock(self):
    self.client.force_login(self.manager)
    response = self.client.post(self.url, self.data)

    self.assertRedirects(response, reverse('product_list'))

    product = Product.objects.get(sku='OPEN001')
    self.assertEqual(product.on_hand, 25)

    movement = StockMovement.objects.get(product=product)
    self.assertEqual(movement.movement_type, 'opening')
    self.assertEqual(movement.quantity_delta, 25)
    self.assertEqual(movement.balance_before, 0)
    self.assertEqual(movement.balance_after, 25)
    self.assertEqual(movement.created_by, self.manager)
```

**Code walkthrough:** `force_login` signs in the test Manager. `client.post` pretends to submit the Add Product form. `assertRedirects` checks that saving takes us back to the product list. `Product.objects.get` reads the saved product from the test database. Each `assertEqual` checks that the stored value matches what we expected. The final check confirms the opening movement belongs to the Manager who created it.

**Filename: `catalog/tests.py`**

The six other test methods are:
- `test_zero_opening_stock`: zero is allowed; no movement is written.
- `test_negative_opening_stock_rejected`: a negative quantity does not create a product.
- `test_missing_opening_stock_rejected`: leaving the required box empty does not create a product.
- `test_opening_stock_not_on_edit_form`: the Edit Product page must not offer the opening-stock input.
- `test_sales_cannot_create_product`: Sales Staff cannot register products.
- `test_failed_stock_record_rolls_back_product`: if recording stock fails, the product creation is undone.

**Filename: `catalog/tests.py`**
```python
def test_failed_stock_record_rolls_back_product(self):
    self.client.force_login(self.manager)

    with patch(
        'catalog.views.change_stock',
        side_effect=ValueError('Simulated stock failure')
    ):
        with self.assertRaisesMessage(
            ValueError, 'Simulated stock failure'
        ):
            self.client.post(self.url, self.data)

    self.assertFalse(Product.objects.filter(sku='OPEN001').exists())
    self.assertEqual(StockMovement.objects.count(), 0)
```

**Code walkthrough:** `patch` temporarily replaces `change_stock` with a version that deliberately fails. This lets us test what happens when something goes wrong, without breaking the real function. The final two checks prove that neither an incomplete product nor a stray stock record was left behind.

**A test mistake we corrected:** Initially, the edit-form test looked for the words `Opening Stock` anywhere on the page. It failed because the supplier was named `Opening Stock Supplier`, even though the actual input was correctly hidden. The test was changed to look for a field-name string instead.

**Filename: `catalog/tests.py`**
```python
self.assertNotContains(response, 'name="Opening Stock"')
```

**Important precision:** This is the exact assertion found in source commit `75fce94`. It passed, but it is **not the strongest possible check**, because the actual HTML field name would be `opening_stock`, not `Opening Stock`. A later test-hardening change should assert `name="opening_stock"`. We do not silently claim the stronger check was committed.

**How we checked it:** The user first reported one failing test caused by the supplier's name. After changing the assertion, `python manage.py test catalog.tests.ProductOpeningStockTests` ran **7 tests successfully**.

**Next:** Run the full project test suite and publish the verified code.

<a id="step-283"></a>
### Step 283 — Verify the entire project and publish Chapter 9

**Before this step:** The new tests passed on their own, but we also needed to make sure Opening Stock did not break older account, supplier, product or inventory features.

**What we did and why:** We ran all project tests and committed only the three files changed for this chapter. A **Git commit** is a saved version of the code; **push** copies that saved version to GitHub.

**Filename: `catalog/forms.py`** — Added the opening-stock form field and removed it in edit mode.

**Filename: `catalog/views.py`** — Connected product creation to the stock service inside a transaction.

**Filename: `catalog/tests.py`** — Added seven tests and corrected the failing assertion.

**Verification commands (PowerShell):**
```powershell
python manage.py check
python manage.py test
git add -- catalog/forms.py catalog/views.py catalog/tests.py
git diff --cached --stat
git commit -m "Module 2 Chapter 9: Add opening stock during product registration"
git push origin main
git rev-parse --short HEAD
```

**How we checked it:** The user reported **zero system-check issues**, **87 tests passing**, and a successful push to `main`. The final commit was [`75fce94`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/75fce94), containing the three intended files. The browser workflow was also confirmed.

**Next:** Chapter 10 should follow the approved Module 2 plan. Do not add a separate stock-changing feature merely because the Opening Stock form is complete.
