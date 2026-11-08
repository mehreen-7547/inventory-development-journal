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

## Step 44: Installing AI Dependencies

#### Step 54 — Install the remaining AI libraries

Run:

```powershell
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt
```

**What this command means:**

- `python -m pip` — runs the package installer using your active Python environment.
- `install` — installs packages.
- `-r` — tells pip to read package requirements from a file.
- `requirements-ai.txt` — contains the AI libraries and their required versions.

The file specifies:

| Library | Purpose |
|---|---|
| `torch` | Runs machine-learning computations |
| `chronos-forecasting` | Provides access to pretrained forecasting models |
| `psutil` | Measures memory usage during testing |

These are free, locally installed libraries. No paid API is required.

**Expected result:** `Successfully installed ...` or `Requirement already satisfied ...`.

Send the terminal output when installation finishes.

#### How we checked it

#### Step 55 — Verify the installed libraries

Assuming Step 54 completed successfully, run:

```powershell
python -c "import torch, chronos, psutil; print('Torch:', torch.__version__)"
```

**What this command does:**

- `python -c` — executes Python code directly from the terminal.
- `import torch, chronos, psutil` — checks whether Python can load all three libraries.
- `print(...)` — displays the installed PyTorch version.

**Expected result:** Something similar to:

```text
Torch: 2.10.0+cpu
```

If an import fails, Python will display an error identifying the problem.

Send the output. Next, we'll run our first forecasting model on your laptop.

#### How we checked it

**Step 55 passed.** All three libraries imported successfully.

The message about Windows not supporting redirects is a PyTorch informational warning. It does not affect our planned forecasting test.

#### Step 56 — Run the first AI model

We'll test **Chronos-Bolt Tiny** before trying the larger Chronos-2 model.

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py --model bolt
```

**What happens:**

1. Downloads the pretrained model (first run only).
2. Creates 90 days of synthetic sales data.
3. Uses the model to forecast the next 30 days.
4. Measures execution time and peak memory usage.
5. Saves results to `ai_probe_results.json`.

No model training or paid API is involved.

**Expected output:** `WORKED: YES`, followed by timing, memory usage, and forecast values.

The first run may take longer because it downloads the model.

Send the complete terminal output so we can evaluate the results before testing Chronos-2.

#### How we checked it

#### Step 56 — Passed

 Chronos-Bolt Tiny worked successfully

Your laptop successfully ran the pretrained forecasting model using only its CPU.

| Measurement | Your result |
|---|---:|
| Total execution time | 24.85 seconds |
| Peak process memory | 379.3 MB |
| Forecast computation | 0.07 seconds |
| Forecast horizon | 30 days |

**Understanding the first day's forecast:**

        P10
        7.37

        P50
        8.42

        P90
        9.39

  P10, P50 and P90 represent the model's 10th, 50th and 90th percentile predictions, respectively.

The 24.85 seconds includes the initial download and loading overhead. Actual forecast computation took only 0.07 seconds.

**Important:** This proves the model runs on your laptop, not that its predictions are accurate enough for inventory decisions. We'll evaluate accuracy separately.

#### Step 57 — Test both forecasting models

Run:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py
```

This runs both models sequentially:

1. Chronos-Bolt Tiny — already downloaded, so it should load faster.
2. Chronos-2 — first run requires downloading its weights and may consume substantially more memory.

The script will overwrite `ai_probe_results.json` with a new file containing **both model results**, preserving Bolt's measurements from this new run.

Send the complete output. We'll compare their feasibility before selecting a model for the project.

*Previous: Step 43 (Understanding python -m)  |  Next: Step 45 (Documenting Model Results)*

---
