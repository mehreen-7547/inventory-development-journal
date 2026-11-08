# Module 1, Chapter 1: Accounts app and custom user model

## Step 56: Approved — Begin Development

Claude has **approved Module 1**, subject to the stated corrections. No further approval is needed.

#### Final role-permission matrix

| Action | Admin | Manager | Sales |
|---|:---:|:---:|:---:|
| Login, logout, change own password | Yes | Yes | Yes |
| View own dashboard | Yes | Yes | Yes |
| Access Django admin | Yes | No | No |
| Create, deactivate or reset user accounts | Yes | No | No |
| Change another user's role | Yes | No | No |
| Manage suppliers, including deactivate/reactivate | No | Yes | No |
| Manage products, including deactivate/reactivate | No | Yes | No |
| View active products and stock | No | Yes | Yes* |
| Record opening stock | No | Yes | No |
| Adjust stock and view movement history | No | Yes | No |
| Record sales | No | No | Yes |
| View own sales | No | N/A | Yes |
| View all sales | No | Yes | No |
| Void sales | No | Yes | No |
| Create and edit draft purchase orders | No | Yes | No |
| Order, cancel or close purchase orders | No | Yes | No |
| Edit expected date of an ordered PO | No | Yes | No |
| Record planned or unplanned receipts | No | Yes | No |
| View low-stock and overdue-order reports | No | Yes | No |
| Import historical sales CSV | No | Yes | No |
| Set forecasting service level | No | Yes | No |
| Generate forecasts and recommendations | No | Yes | No |
| Accept, defer or dismiss recommendations | No | Yes | No |
| Create draft PO from accepted recommendation | No | Yes | No |
| Mark anomalies Open / Reviewed / Expected | No | Yes | No |
| View business reports | No | Yes | No |
| Automatic ordering | Not available | Not available | Not available |

*Sales Staff can see only active products' SKU, name, price and available quantity.*

The Admin dashboard will show only a user count and a Django admin link. Sales Staff must ask a Manager to void sales. Every restricted URL will have server-side permission enforcement and tests.

The app structure remains the approved **10 core tables and 4 stretch tables**. `core` will contain only homepage/dashboard views, with no models or business logic.

#### Step 81 — Create the `accounts` app

In your VS Code PowerShell terminal, run:

```powershell
cd .\techmindz_inventory_module0_sqlite_atomic
python manage.py startapp accounts
```

**Expected result:** A new `accounts` folder appears with files such as `models.py`, `views.py` and `admin.py`.

This is the standard Django app-creation process used in your training.

Do not run migrations or delete the database yet. We'll configure the custom user model first and verify the existing data before the approved reset.

*Previous: Step 55 (Publish Completion Tag)  |  Next: Step 57 (Custom User Model)*

---
