# MODULE 0 — Getting started

## Chapter 6: AI forecasting feasibility test (Steps 42–45)

### Before we begin

The eventual inventory website is meant to forecast demand, but we had not yet built that part of the website.

### What we worked on

We prepared the machine-learning libraries and tried early experiments to see whether forecasting tools could run on this computer.

### Picture it this way

A small experiment is not the same as a finished forecasting feature. These notes tell us what we explored, not that customers can already see predictions on a dashboard.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 42: Installing PyTorch](#step-42)
- [Step 43: Understanding python -m](#step-43)
- [Step 44: Installing AI Dependencies](#step-44)
- [Step 45: Documenting Model Results](#step-45)

---

<a id="step-42"></a>

### Step 42: Installing PyTorch

**What was happening before this step?**

The forecasting experiment needed a library called PyTorch, but it was not installed yet.

**What did we do, and what does it mean?**

PyTorch provides functions used by many machine-learning models. Installing it gives us a tool for experiments; it does not train a model or guarantee that predictions will be good.

**Code or command from this step**

*1. Run.*

```powershell
python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
```

**How did we know what happened?**

Our notes proceeds to the remaining AI packages after this installation step.

**Where to go next:** Previous: [Step 41 — Record and Push Test Results](05-security-checks.md#step-41) · Next: [Step 43 — Understanding python -m](06-ai-forecasting-feasibility-test.md#step-43)


---

<a id="step-43"></a>

### Step 43: Understanding python -m

**What was happening before this step?**

We used commands such as `python -m pip`, and wanted to know why the word `python` was included.

**What did we do, and what does it mean?**

`pip` installs Python packages. The command `python -m pip` explicitly asks the current Python installation to run its own installer, reducing the chance that packages go into the wrong Python environment.

**Code or command from this step**

*1. Runs the pip command directly.*

```powershell
python -m pip install torch
```

**How did we know what happened?**

This was a concept explanation rather than a separate package installation.

**Where to go next:** Previous: [Step 42 — Installing PyTorch](06-ai-forecasting-feasibility-test.md#step-42) · Next: [Step 44 — Installing AI Dependencies](06-ai-forecasting-feasibility-test.md#step-44)


---

<a id="step-44"></a>

### Step 44: Installing AI Dependencies

**What was happening before this step?**

The experiment required additional ready-made libraries beyond PyTorch.

**What did we do, and what does it mean?**

The AI requirements file listed those extra packages. Installing them prepared the testing environment, but we still had to run experiments and inspect results before deciding what approach was suitable.

**Code or command from this step**

*1. Run.*

```powershell
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt
```

**How did we know what happened?**

Our notes includes follow-up dependency checks and subsequent attempts to run the models.

**Where to go next:** Previous: [Step 43 — Understanding python -m](06-ai-forecasting-feasibility-test.md#step-43) · Next: [Step 45 — Documenting Model Results](06-ai-forecasting-feasibility-test.md#step-45)


---

<a id="step-45"></a>

### Step 45: Documenting Model Results

**What was happening before this step?**

After running early model tests, we wanted to record the findings instead of relying on memory.

**What did we do, and what does it mean?**

A feasibility result answers “can we run this kind of tool here?” It does not yet answer “does our finished inventory website make reliable predictions?” We kept that difference clear in the notes.

**Code or command from this step**

*1. Code or command used at this point.*

```powershell
Add-Content -Path "C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

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

*2. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

*3. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record AI forecasting feasibility results"
```

*4. Code or command used at this point.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How did we know what happened?**

Our notes describes model outcomes and checks, but the journal should distinguish those observations from future model selection.

**Where to go next:** Previous: [Step 44 — Installing AI Dependencies](06-ai-forecasting-feasibility-test.md#step-44) · Next: [Step 46 — Update Module 0 Report](07-module-0-completion.md#step-46)

---

### The point of this chapter

A small experiment is not the same as a finished forecasting feature. These notes tell us what we explored, not that customers can already see predictions on a dashboard.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

