# Module 0, Chapter 6: AI forecasting feasibility test

## Step 42: Installing PyTorch

Your latest journal commit is verified on GitHub. All five Django tests have passed.

We're now moving to the **AI feasibility testing** portion of Module 0.

#### Step 53 — Install PyTorch (CPU version)

Our project will test two free, pretrained forecasting models: **Chronos-Bolt Tiny** and **Chronos-2**.

Both use PyTorch, a Python library for running machine-learning models.

Run:

```powershell
python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
```

**Command explanation:**

- `python -m pip` — runs Python's package installer.
- `install` — installs a package.
- `torch==2.10.0` — installs the specified PyTorch version.
- `--index-url` — selects PyTorch's official CPU-only package repository.

This is free, requires no API key, and does not require a graphics card. The download may be large.

**Expected result:** `Successfully installed torch-2.10.0` (possibly alongside dependencies).

Send the terminal output after installation. We'll install the remaining AI dependencies next.

*Previous: Step 41 (Record and Push Test Results)  |  Next: Step 43 (Understanding python -m)*

---

## Step 43: Understanding python -m

The `-m` in `python -m pip` stands for **module**.

It tells Python to run a Python module as a program.

#### Compare

```powershell
pip install torch
```

Runs the `pip` command directly.

```powershell
python -m pip install torch
```

Tells Python to find the `pip` module and run it.

#### Why do we prefer `python -m pip`?

Your computer may have multiple Python installations. Using `python -m pip` ensures that **pip belongs to the Python interpreter you're currently running**.

For example:

- `python` — selects your active virtual environment's Python.
- `-m pip` — runs that Python's pip module.
- `install torch` — asks pip to install PyTorch.

**Remember:** `-m` is an option belonging to Python, not pip. It can run other modules too, such as `python -m django --version`.

We're still at **Step 53**; no additional command is needed until PyTorch installation finishes.

*Previous: Step 42 (Installing PyTorch)  |  Next: Step 44 (Installing AI Dependencies)*

---
