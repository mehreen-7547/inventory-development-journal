# MODULE 2 — Supplier and inventory foundations

## Chapter 6: Edit Product (Journal Step 273)

### Before this chapter
Chapter 5 created the Product model, list and registration form. There was no way to correct a product's name, price, supplier or planning settings after saving it.

### What we built
A Manager-only Edit Product mode on the catalogue and a shared create/edit form. **Interactive Step 407 corresponds to journal Step 273.** Source published together with Chapter 5 in commit [`796ccb5`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/796ccb5).

<a id="step-273"></a>
### Step 273: Edit saved product details without changing stock
**Before this step:** The Add Product form always created a new record.

**What we did and why:** A Manager selects Edit Product, clicks a product name, and edits the existing record. The instance argument tells ModelForm to update that row instead of inserting another. An inactive supplier already attached to a product remains selectable so that editing its other fields is possible. The allowed fields still exclude on_hand; changing a price cannot silently alter inventory.

**Filename: `catalog/views.py`**
```python
@login_required
@role_required('manager')
def product_edit(request, product_id):
    product = get_object_or_404(Product, id=product_id)
    if request.method == 'POST':
        form = ProductForm(request.POST, instance=product)
        if form.is_valid():
            form.save()
            return redirect('product_list')
    else:
        form = ProductForm(instance=product)
    return render(request, 'catalog/product_form.html', {'form': form})
```
The existing product_list view also sets edit_mode only when the current user is a Manager and the query string is `?mode=edit`.

**Filename: `catalog/forms.py`**
```python
def __init__(self, *args, **kwargs):
    super().__init__(*args, **kwargs)
    suppliers = Supplier.objects.filter(is_active=True)
    if self.instance.pk:
        suppliers = (
            Supplier.objects.filter(is_active=True)
            | Supplier.objects.filter(pk=self.instance.supplier_id)
        )
    self.fields['supplier'].queryset = (
        suppliers.distinct().order_by('name')
    )
```
The queryset is restricted to active suppliers, plus the product's current supplier when editing. `distinct()` prevents duplicate options.

**Filename: `catalog/urls.py`**
```python
path(
    'products/<int:product_id>/edit/',
    views.product_edit,
    name='product_edit'
),
```
**Filename: `catalog/templates/catalog/product_list.html`**
```html
{% if request.user.role == 'manager' %}
    {% if edit_mode %}
        <a href="{% url 'product_list' %}">Cancel Edit</a>
    {% else %}
        <a href="{% url 'product_list' %}?mode=edit">Edit Product</a>
    {% endif %}
{% endif %}
{% if edit_mode %}
    <a href="{% url 'product_edit' product.id %}">{{ product.name }}</a>
{% else %}
    {{ product.name }}
{% endif %}
```
**Filename: `catalog/templates/catalog/product_form.html`**
```html
{% if form.instance.pk %}
    <h2>Edit Product</h2>
{% else %}
    <h2>Add Product</h2>
{% endif %}
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    {% if form.instance.pk %}
        <button type="submit">Update Product</button>
    {% else %}
        <button type="submit">Save Product</button>
    {% endif %}
    <a href="{% url 'product_list' %}">Cancel</a>
</form>
```
**Filename: `catalog/tests.py`** — three tests verify that Managers can edit, Sales Staff cannot edit, and an existing inactive supplier remains available. The Manager edit test also asserts on_hand remains zero.

**How we checked it:** The user reported all 15 catalogue tests and seven browser checks passed. After publishing, `python manage.py check` again reported no issues and `python manage.py test catalog` passed all 15 tests. The source push to `main` succeeded at `796ccb5` and the source working tree was clean.

**Next:** Module 2 Chapter 7 — Activate and Deactivate Product (not yet implemented).
