# Module 4 — Chapter 4: Sales History

**Journal Steps 322–326 · Interactive Steps 461–462**  
**Source:** [commit 664cf33](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/664cf33) on `main`. User verified **12 history, 71 Sales, 182 full-project tests**; whitespace checks passed and source push succeeded.

## Before this chapter

Chapter 3 let staff record a sale and see its confirmation, but there was no place to browse past sales. This chapter adds a searchable, filtered, paginated history page. Sales staff see only their own records; managers see all. No voiding service or stock reversal is implemented here.

<a id="step-322"></a>
## Step 322 — Create the protected history view

### Before this step
A sale existed in the database but no list view exposed past transactions.

### What we did and why
We created `sale_list()`, protected by login and sales/manager role checks. We load each sale's creator efficiently and show newest records first.

**Filename: `sales/views.py`** — exact source excerpt:
```python
@login_required
@role_required('sales', 'manager')
def sale_list(request):
    sales = Sale.objects.select_related(
        'created_by'
    ).order_by('-created_at', '-pk')

    if request.user.role == 'sales':
        sales = sales.filter(created_by=request.user)
```

### Code walkthrough
The decorators reject anonymous and unauthorized users. `select_related('created_by')` retrieves employee information with the sale query. `order_by` sorts newest first and uses the primary key to break timestamp ties. The role-specific filter happens before searching, preventing a sales employee from finding someone else's sale.

### How we checked it
Tests confirm anonymous and admin restrictions, own-sale visibility and manager-wide visibility.

### Next
Add search, status filtering and pagination.

<a id="step-323"></a>
## Step 323 — Search, filter and paginate sales

### Before this step
A growing sales list would be difficult to navigate without filtering and page limits.

### What we did and why
We added case-insensitive search for sale number or employee username, status filtering for completed/voided sales, and ten results per page.

**Filename: `sales/views.py`** — exact source excerpt:
```python
    search = request.GET.get('search', '').strip()
    status = request.GET.get('status', '').strip()

    if search:
        sales = sales.filter(
            Q(sale_number__icontains=search)
            | Q(created_by__username__icontains=search)
        )

    if status in ['completed', 'voided']:
        sales = sales.filter(status=status)
    else:
        status = ''

    paginator = Paginator(sales, 10)
    page_obj = paginator.get_page(request.GET.get('page'))
```

### Code walkthrough
`Q(... ) | Q(...)` means match either searchable field. The status whitelist ignores unknown status values. `Paginator` splits the already-authorized queryset into groups of ten; `get_page()` handles invalid page inputs.

### How we checked it
Tests cover sale-number search, employee search, valid and invalid status, pagination and attempts to search for another employee's sale.

### Next
Connect the view to a URL and page.

<a id="step-324"></a>
## Step 324 — Register the URL and build the history template

### Before this step
The new view needed a route and a page where users could enter filters and browse records.

### What we did and why
We registered `/sales/` and created a new template with a GET filter form, table, empty-state message, detail links and previous/next navigation.

**Filename: `sales/urls.py`** — exact source excerpt:
```python
    path(
        'sales/',
        views.sale_list,
        name='sale_list'
    ),
```

**Filename: `sales/templates/sales/sale_list.html`** — exact source excerpt:
```html
{% for sale in page_obj %}
<tr>
    <td>{{ sale.sale_number }}</td>
    <td>{{ sale.created_at|date:"d M Y, H:i" }}</td>
    <td>{{ sale.created_by.username }}</td>
    <td>{{ sale.get_status_display }}</td>
    <td>{{ sale.total }}</td>
    <td>
        <a href="{% url 'sale_detail' sale.pk %}">
            View
        </a>
    </td>
</tr>
{% empty %}
<tr>
    <td colspan="6">No sales found.</td>
</tr>
{% endfor %}
```

### Code walkthrough
The URL name `sale_list` is used by templates instead of hardcoding addresses. Django iterates only the current page. The `empty` branch displays a clear message when filters return no rows. Pagination links preserve search and status; the search term is URL-encoded.

### How we checked it
Source commit contains the route and new template; the history tests render the page and inspect its results.

### Next
Link the history page from existing navigation.

<a id="step-325"></a>
## Step 325 — Connect dashboards and sale details

### Before this step
Even a working history page is hard to discover without links from the existing workflow.

### What we did and why
We added Sales History links to manager and sales dashboards and a return link from the sale detail page.

**Filename: `core/templates/core/home.html`** — exact source excerpt:
```html
<a href="{% url 'sale_list' %}"
   class="btn btn-primary">
    Sales History
</a>
```

**Filename: `sales/templates/sales/sale_detail.html`** — exact source excerpt:
```html
<a href="{% url 'sale_list' %}">
    Back to Sales History
</a>
```

### Code walkthrough
The dashboard link is present for the manager and sales roles, not the admin role. The detail page links back to the same history route. Actual permissions remain enforced by the view, not merely by hiding a link.

### How we checked it
Source shows both navigation changes; role tests confirm the history page's access control.

### Next
Add regression tests and publish.

<a id="step-326"></a>
## Step 326 — Test, fix fixtures and publish

### Before this step
New permissions, filtering and pagination required regression tests. The first test run failed before reaching the views because test users lacked distinct emails.

### What we did and why
We added `SaleHistoryTests` with 12 tests, gave each fixture user a unique email, and corrected one assertion that mistook the search input's echoed value for a result row. We removed trailing whitespace, then committed six files.

**Filename: `sales/tests.py`** — exact source excerpt:
```python
    def test_sales_user_cannot_bypass_filter(self):
        self.client.force_login(self.sales_user)

        response = self.client.get(reverse('sale_list'), {
            'search': 'HISTORY-002'
        })

        self.assertEqual(response.status_code, 200)
        self.assertEqual(
            len(response.context['page_obj'].object_list),
            0
        )
        self.assertContains(response, 'No sales found.')
```

### Code walkthrough
A search term appears in the HTML input even when there are no matching rows, so testing the entire response for the absence of `HISTORY-002` was incorrect. The corrected assertion checks the actual paginated queryset. The test fixtures also respect the custom user's unique-email database constraint.

### How we checked it
User terminal confirmed 12/12 history tests, 71/71 Sales tests and 182/182 total tests. `git diff --check` and staged whitespace checks passed after CRLF-safe cleanup. Source commit `664cf33` was pushed to `main`; the working tree was clean.

### Next
Module 4 Chapter 5: implement sale voiding and stock reversal only after reviewing the approved workflow.
