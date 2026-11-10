# Module 3 — Chapter 2: Stock Ledger Integrity

**Journal Steps 291–294 · Interactive Steps 424–427**  
**Verified source:** [commit `5d662eb`](https://github.com/mehreen-7547/ai-assisted-inventory-manager/commit/5d662eb) on `main`.  
**Files:** `inventory/management/__init__.py` (NEW), `inventory/management/commands/__init__.py` (NEW), `inventory/management/commands/check_ledger.py` (NEW), `inventory/tests.py` (CLASS: extended).

## Before this chapter

Module 2 established `Product.on_hand`, `StockMovement` and the shared `change_stock()` service. Module 3 Chapter 1 added physical stock adjustments. Those operations write stock balances and movement history together, but we still needed a way to **check existing records** for inconsistencies without modifying them. Imagine the product says 15 notebooks but its history ends at 10: the manager needs a warning, not an automatic correction.

<a id="step-291"></a>
## Step 291 — Review the existing stock ledger (interactive Step 424)

### Before this step
The application already records a movement's starting balance, signed change and ending balance, and stores the current quantity on the product.

### What we did and why
We inspected `inventory/models.py`, `inventory/services.py` and `inventory/tests.py` at source commit `983d4b0`. We identified three rules to check: movement continuity, movement arithmetic and agreement between the final ledger balance and `Product.on_hand`.

**Filename: `inventory/models.py`** — relevant existing fields:
```python
quantity_delta = models.IntegerField()
balance_before = models.PositiveIntegerField()
balance_after = models.PositiveIntegerField()
```

### Code walkthrough
`quantity_delta` can be positive (stock added) or negative (stock removed). `balance_before` is the stock immediately before that operation; `balance_after` is immediately after. For example, 10 + (-3) = 7. A new movement should begin at the previous movement's ending balance.

### How we checked it
We read the existing model and service from GitHub. This was a review step: no code was changed.

### Next
Create a read-only Django command that checks all products and their movement histories.

<a id="step-292"></a>
## Step 292 — Add the `check_ledger` command (interactive Step 425)

### Before this step
Stock adjustments worked, but there was no single command to inspect the consistency of every product's movement history.

### What we did and why
We added the standard Django management-command folder and a command named `check_ledger`. It reads each product's movements in chronological order, collects errors and fails if any issue exists. It **does not update the database**.

**Filename: `inventory/management/__init__.py`** — empty package file (NEW).  
**Filename: `inventory/management/commands/__init__.py`** — empty package file (NEW).

**Filename: `inventory/management/commands/check_ledger.py`** — complete published command (NEW):
```python
from django.core.management.base import BaseCommand, CommandError

from catalog.models import Product
from inventory.models import StockMovement


class Command(BaseCommand):
    help = 'Check stock movement history against product balances'

    def handle(self, *args, **options):
        errors = []

        for product in Product.objects.all().order_by('pk'):
            movements = StockMovement.objects.filter(
                product=product
            ).order_by('created_at', 'pk')

            previous_balance = 0

            for movement in movements:
                if movement.balance_before != previous_balance:
                    errors.append(
                        f'{product.sku}: movement {movement.pk} '
                        'has an incorrect starting balance'
                    )

                expected_after = (
                    movement.balance_before + movement.quantity_delta
                )

                if movement.balance_after != expected_after:
                    errors.append(
                        f'{product.sku}: movement {movement.pk} '
                        'has incorrect balance arithmetic'
                    )

                previous_balance = movement.balance_after

            if product.on_hand != previous_balance:
                errors.append(
                    f'{product.sku}: product stock is '
                    f'{product.on_hand}, but ledger shows '
                    f'{previous_balance}'
                )

        if errors:
            for error in errors:
                self.stderr.write(self.style.ERROR(error))

            raise CommandError(
                f'Ledger check failed: {len(errors)} issue(s) found'
            )

        self.stdout.write(
            self.style.SUCCESS('Stock ledger check passed')
        )
```

### Code walkthrough
`BaseCommand` is Django's way of making a command runnable through `manage.py`. `handle()` is the function Django calls. `Product.objects.all()` visits every product, while `StockMovement.objects.filter(product=product)` retrieves only its history. Sorting by time and primary key keeps the check in a consistent order. `previous_balance = 0` means the first movement should begin at zero. Each loop compares the starting balance with the previous ending balance, then checks the arithmetic. Finally, the command compares the product's stored stock with the last ending balance. Errors are printed to stderr and `CommandError` signals failure; success prints a clear message.

**Important limitation:** This command checks recorded arithmetic and continuity, not whether physical stock actually matches the shelf. A product with no movements is expected to have stock zero.

### How we checked it
The exact command file was independently fetched from source commit `5d662eb`. Django's four command tests passed according to the user's terminal confirmation.

### Next
Test both valid and deliberately inconsistent histories.

<a id="step-293"></a>
## Step 293 — Test the command (interactive Step 426)

### Before this step
The command existed, but we needed evidence that it accepted a correct ledger and rejected several types of incorrect ledger.

### What we did and why
We extended the existing Django test file with four focused tests. Tests create temporary records, including intentionally corrupted balances, and expect the command to raise `CommandError` when something is wrong.

**Filename: `inventory/tests.py`** — new imports:
```python
from io import StringIO
from django.core.management import call_command
from django.core.management.base import CommandError
```

**Filename: `inventory/tests.py`** — representative test from `StockLedgerCommandTests`:
```python
def test_incorrect_product_balance_fails(self):
    # Product stock must match its movement history.
    change_stock(
        self.product.pk, 10, 'opening',
        'Initial stock', self.manager
    )

    Product.objects.filter(
        pk=self.product.pk
    ).update(on_hand=15)

    with self.assertRaises(CommandError):
        call_command(
            'check_ledger',
            stdout=StringIO(),
            stderr=StringIO()
        )
```

### Code walkthrough
`change_stock()` creates a valid movement of +10. The test then deliberately changes the product's stored quantity to 15 without adding a movement, simulating a broken record. `call_command('check_ledger')` runs the same command a developer can run in the terminal. `assertRaises(CommandError)` confirms the command detects the mismatch. `StringIO` captures its printed output during tests. The other tests check a valid history, broken movement arithmetic and broken continuity. Django's test database keeps these deliberate corruptions away from real inventory data.

### How we checked it
The user confirmed the focused command tests reported **four passing tests**. The full source test file at commit `5d662eb` contains `StockLedgerCommandTests` and the previously added `StockLedgerIntegrityTests` (two continuity/reconciliation tests). The full project test suite later reported **111 tests, OK**.

### Next
Run the final checks, fix any formatting warnings and publish the source.

<a id="step-294"></a>
## Step 294 — Verify and publish Chapter 2 (interactive Step 427)

### Before this step
The command and tests were written locally. They still needed package files, a clean staged diff and a successful source push.

### What we did and why
The first guarded script stopped because `inventory/management/__init__.py` was missing. We created the two empty `__init__.py` files and confirmed the folder contained all three expected files. The next Git check found extra blank lines at the ends of `check_ledger.py` and `inventory/tests.py`. We removed those blank lines, staged the changes and reran the checks.

**Filename: terminal (PowerShell)** — final verification and publish actions:
```powershell
git diff --cached --check
python manage.py test
git commit -m "Module 3 Chapter 2: Stock ledger integrity checks"
git push origin main
git log -1 --oneline
git status --short
```

### Code walkthrough
`git diff --cached --check` checks staged changes for formatting problems. `python manage.py test` runs Django's full test suite. `git commit` saves a local checkpoint; `git push` uploads it to GitHub. The final two commands show which commit was saved and whether any changes remain locally.

### How we checked it
The user pasted the final terminal output: **111 tests passed**, commit `5d662eb` was created and pushed to `main`, and `git status --short` produced no entries. The GitHub commit lists exactly four changed files: the two empty package files, `check_ledger.py`, and `inventory/tests.py`. Line-ending conversion warnings occurred earlier but did not block the final commit.

### Next
Chapter 2 is complete only after the `study-2` journal chapter and its indexes/handoff are published and checked. The next planned development area is Module 4 (Sales), not an invented extra inventory feature.
