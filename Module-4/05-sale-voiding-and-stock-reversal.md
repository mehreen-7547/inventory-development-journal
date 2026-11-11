# Module 4 — Chapter 5: Sale Voiding and Stock Reversal

**Journal Steps 327–331 · Interactive Steps 463–467**  
**Source:** [commit f202c39](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/f202c39), pushed to `main`. User terminal confirmed clean working tree; test command outputs were not supplied in the final checkpoint.

## Before this chapter
Chapter 4 listed sales and could filter by status, but there was no action to void a sale or restore the stock deducted when it was recorded. The Sale model already had void metadata, and the inventory ledger already supported movement type `void`, so no migration was needed.

<a id="step-327"></a>
## Step 327 — Add the voiding service (interactive Step 463)

### Before this step
Recording a sale deducted inventory. Cancelling a transaction needed to reverse that deduction without deleting the original sale.

### What we did and why
We added `void_sale()` for managers only. It requires an active authenticated manager and a nonblank reason. It loads the sale inside a transaction, rejects a sale that is not completed, restores each line's quantity through the existing stock service, and records who voided it and when.

**Filename: `sales/services.py`** — source excerpt:
```python
def void_sale(sale_id, reason, user):
    if not user or not user.is_authenticated or not user.is_active:
        raise ValueError('An active user is required')

    if user.role != 'manager':
        raise ValueError('Only managers can void sales')

    if not isinstance(reason, str) or not reason.strip():
        raise ValueError('Void reason is required')

    reason = reason.strip()

    with transaction.atomic():
        sale = Sale.objects.select_for_update().get(
            pk=sale_id
        )

        if sale.status != 'completed':
            raise ValueError('Sale is already voided')

        for line in sale.lines.all():
            change_stock(
                product_id=line.product_id,
                delta=line.quantity,
                movement_type='void',
                reason=reason,
                user=user,
                source=sale.sale_number
            )
```

### Code walkthrough
`delta=line.quantity` is positive, the opposite of the negative quantity recorded for a sale. `source=sale.sale_number` connects the stock movement to the transaction. `transaction.atomic()` keeps the reversal and status update in one database transaction. `select_for_update()` requests row locking on supporting databases; SQLite does not provide equivalent row-level locking.

**Filename: `sales/services.py`** — source excerpt:
```python
        sale.status = 'voided'
        sale.voided_by = user
        sale.voided_at = timezone.now()
        sale.void_reason = reason

        sale.save(update_fields=[
            'status',
            'voided_by',
            'voided_at',
            'void_reason'
        ])

    return sale
```

### How we checked it
The service was included in the source commit. Automated tests were added later in this chapter; their terminal results were not shared in the final checkpoint.

### Next
Add a manager-only confirmation page.

<a id="step-328"></a>
## Step 328 — Add the manager confirmation form and view (interactive Step 464)

### Before this step
The backend service could void sales, but users had no safe interface to enter a reason and confirm the operation.

### What we did and why
We added a required text-area reason form and a manager-only view. GET displays the confirmation page; POST validates the reason, calls the service, displays a success message and redirects to sale details. Service validation errors appear on the form.

**Filename: `sales/forms.py`** — source excerpt:
```python
class VoidSaleForm(forms.Form):
    reason = forms.CharField(
        label='Reason for voiding',
        widget=forms.Textarea(attrs={'rows': 3}),
        max_length=500
    )

    def clean_reason(self):
        reason = self.cleaned_data['reason'].strip()

        if not reason:
            raise forms.ValidationError('Reason is required')

        return reason
```

**Filename: `sales/views.py`** — source excerpt:
```python
@login_required
@role_required('manager')
def sale_void(request, sale_id):
    sale = get_object_or_404(Sale, pk=sale_id)

    if request.method == 'POST':
        form = VoidSaleForm(request.POST)

        if form.is_valid():
            try:
                void_sale(
                    sale_id=sale.pk,
                    reason=form.cleaned_data['reason'],
                    user=request.user
                )
```

### Code walkthrough
The decorator checks permissions on the server, not just in the page. The form enforces a maximum reason length and strips whitespace. The service repeats authorization and state checks, protecting callers outside the view. A GET request does not change the sale.

### How we checked it
The view and form are in source commit `f202c39`. Tests cover the manager page, anonymous redirect, denied sales access and POST behavior.

### Next
Connect the URL, confirmation template and sale detail actions.

<a id="step-329"></a>
## Step 329 — Connect the URL and sale detail interface (interactive Step 464)

### Before this step
The view needed a named route and templates to present the decision and show its result.

### What we did and why
We added a `sale_void` URL, a new confirmation page and a conditional Void Sale link for managers viewing completed sales. Voided sales show the manager, timestamp and reason in their detail page.

**Filename: `sales/urls.py`** — source excerpt:
```python
    path(
    'sales/<int:sale_id>/void/',
    views.sale_void,
    name='sale_void'
    ),
```

**Filename: `sales/templates/sales/sale_void.html`** — source excerpt:
```html
{% if sale.status == 'completed' %}

<p>
    Voiding this sale will restore its product quantities
    to inventory. This action cannot be reversed.
</p>

<form method="post">
    {% csrf_token %}

    {{ form.as_p }}

    <button type="submit" class="btn btn-danger">
        Confirm Void
    </button>
</form>
```

**Filename: `sales/templates/sales/sale_detail.html`** — source excerpt:
```html
{% if user.role == 'manager' and sale.status == 'completed' %}

<p>
    <a href="{% url 'sale_void' sale.pk %}"
       class="btn btn-danger">
        Void Sale
    </a>
</p>

{% endif %}
```

### Code walkthrough
The URL name lets templates refer to the view without hardcoded links. The POST form includes a CSRF token. Hiding the action from other roles is a usability feature; the view and service enforce security.

### How we checked it
The new page and updated detail page were committed. The tests include GET versus POST behavior and permission checks.

### Next
Test the reversal and failure cases.

<a id="step-330"></a>
## Step 330 — Add voiding regression tests (interactive Step 465)

### Before this step
A void could incorrectly restore stock twice, omit audit information, or allow an unauthorized user to cancel a sale.

### What we did and why
We added `VoidSaleTests` with 14 tests covering stock restoration, ledger movement, audit metadata, repeat void rejection, role restrictions, blank reason rejection, GET safety, POST success and failure rollback.

**Filename: `sales/tests.py`** — source excerpt:
```python
    def test_cannot_void_twice(self):
        void_sale(
            self.sale.pk,
            'First void',
            self.manager
        )

        with self.assertRaises(ValueError):
            void_sale(
                self.sale.pk,
                'Second void',
                self.manager
            )
```

**Filename: `sales/tests.py`** — source excerpt:
```python
    def test_void_rolls_back_if_stock_update_fails(self):
        with patch(
            'sales.services.change_stock',
            side_effect=ValueError('Stock update failed')
        ):
            with self.assertRaises(ValueError):
                void_sale(
                    self.sale.pk,
                    'Test rollback',
                    self.manager
                )
```

### Code walkthrough
The duplicate test protects against restoring stock twice. `patch()` simulates a stock-service failure so the test can check that sale status and inventory remain unchanged. The fixtures use unique email addresses to respect the custom user model.

### How we checked it
All 14 tests exist in the pushed source. The user said checks were OK earlier, but the actual test output and counts were not included with the final commit log, so exact pass counts are not asserted here.

### Next
Publish the source checkpoint and record it in the journal.

<a id="step-331"></a>
## Step 331 — Clean whitespace and publish (interactive Steps 466–467)

### Before this step
The chapter had been implemented, but needed a reproducible Git checkpoint and a clean working tree.

### What we did and why
A PowerShell block removed trailing spaces and tabs without altering indentation, staged seven source files, checked staged whitespace, committed and pushed to `main`. The user asked to include this cleanup automatically in future chapter commit blocks.

**Filename: `sales/services.py`** — source excerpt identifying the published operation:
```python
    with transaction.atomic():
        sale = Sale.objects.select_for_update().get(
            pk=sale_id
        )
```

### How we checked it
User terminal confirmed source commit `f202c39`, push `664cf33..f202c39`, seven changed files, and `nothing to commit, working tree clean`. Git warned that LF may be replaced by CRLF on Windows; these are informational warnings. Test counts were not provided in the final terminal log.

### Next
Continue with the next approved development chapter after verifying its scope; keep future journal changes on `study-2` only.
