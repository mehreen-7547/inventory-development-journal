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

## Step 45: Documenting Model Results

Both forecasting models passed the feasibility test. Before testing prediction accuracy, we'll save these results in your private development journal.

#### Step 58 — Append, stage, commit and push

Run the following PowerShell commands in order:

**1. Append the journal entry**

```powershell
Add-Content -Path "C:\Users\mehre\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

## Module 0 — AI Forecasting Feasibility Test

### Objective
Determine whether two pretrained forecasting models can run locally on my laptop without training, paid APIs or cloud computing.

### Models
1. Amazon Chronos-Bolt Tiny
2. Amazon Chronos-2

### Libraries
- PyTorch 2.10.0+cpu
- chronos-forecasting
- psutil

### Commands
python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt
python -c "import torch, chronos, psutil; print('Torch:', torch.__version__)"
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py --model bolt
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py

### Test Data
- 90 days of synthetic daily sales.
- Forecast horizon: 30 days.
- Both models received the same history.
- No model training was performed.

### Results — Both-Model Run

Chronos-Bolt Tiny:
- Status: PASS
- Total execution: 7.30 seconds
- Peak process memory: 363.5 MB
- Forecast computation: 0.01 seconds
- First-day P10/P50/P90: 7.37 / 8.42 / 9.39

Chronos-2:
- Status: PASS
- Total execution: 59.74 seconds
- Peak process memory: 811.8 MB
- Forecast computation: 0.05 seconds
- First-day P10/P50/P90: 8.85 / 9.03 / 9.15

### Interpretation
- Both models successfully generated forecasts on CPU.
- Chronos-Bolt Tiny used less memory in this test.
- Chronos-2's first run included downloading model weights.
- Total execution times are therefore not directly comparable.
- P10, P50 and P90 represent prediction quantiles.
- The results prove technical feasibility, not forecast accuracy.

### Issues
PyTorch displayed an informational warning about Windows redirects. Imports and model execution succeeded.

### Next Steps
- Evaluate prediction accuracy using held-out sales data.
- Compare models against a simple forecasting baseline.
- Select a model based on accuracy and resource requirements.

### Status
PASS — Both pretrained models run locally.
Model selection remains pending.
'@
```

**2. Stage**

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal add inventory-development-journal.md
```

**3. Commit**

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal commit -m "docs: record AI forecasting feasibility results"
```

**4. Push**

```powershell
git -C C:\Users\mehre\projects2026\inventory-development-journal push
```

#### Next: Step 59 — Forecast Accuracy Testing

We'll create a separate evaluation script that compares predictions with actual held-out values, using metrics such as **MAE (Mean Absolute Error)**.

This will help us choose a forecasting model based on evidence rather than simply whether it runs.

Send **done** after pushing the journal update.

#### How we checked it

We'll follow Claude's instructions and **close Module 0 without expanding its scope**.

The decisions are:

- **Default AI model:** Chronos-Bolt Tiny, because it worked successfully with substantially lower memory usage.
- **Forecast accuracy testing:** Deferred to Module 8, including interval coverage.
- **Module 1:** Not started until the architecture and permissions are reviewed and approved.

I also checked your public GitHub repository and confirmed that `config/settings.py` already has `TIME_ZONE = "Asia/Kolkata"` and `USE_TZ = True`. The separate, pinned requirements files, `.env.example`, and README are present. The README still needs its completion status updated. A final Git history security audit remains necessary before tagging.

#### Step 59 — Open the Module 0 report

Run this command in your current VS Code PowerShell terminal:

```powershell
code .\techmindz_inventory_module0_sqlite_atomic\MODULE0_REPORT.md
```

This opens the existing report for editing.

We'll preserve Claude's required **a–h report structure**, replace the pending-verification statements with actual results, and record the Chronos-Bolt Tiny recommendation.

After the report and README are updated, we'll verify the changes, commit them, review the planned app structure and permission table, and only then create the `m0-complete` tag.

Tell me when the report is open.

*Previous: Step 44 (Installing AI Dependencies)  |  Next: Step 46 (Update Module 0 Report)*

---
