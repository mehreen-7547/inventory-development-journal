# MODULE 0 — Foundations

## Chapter 6: AI forecasting feasibility test (Steps 42–45)

**Where we are.** The inventory project is intended to use forecasting. Before promising that feature, we checked whether model libraries could run on the available laptop.

**What this chapter gives us.** Understand the role of PyTorch, isolated packages, and feasibility results; recognize that no forecasting endpoint was deployed yet.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Distinguish a feasibility test from a validated production model.

### Find a step

- [Step 42: Installing PyTorch](#step-42)
- [Step 43: Understanding python -m](#step-43)
- [Step 44: Installing AI Dependencies](#step-44)
- [Step 45: Documenting Model Results](#step-45)

---

<a id="step-42"></a>

### Step 42: Installing PyTorch

**Why we are doing this.** The forecasting experiment relied on PyTorch, which was not yet available in the chosen environment.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
```

**How it works.** `python -m pip install` installs the PyTorch package into the active interpreter. The package supplies tensor operations required by some forecasting models.

**How we checked it.** The source proceeds to the remaining AI packages after this installation step.

**Connects to.** Previous: [Step 41 — Record and Push Test Results](05-security-checks.md#step-41) · Next: [Step 43 — Understanding python -m](06-ai-forecasting-feasibility-test.md#step-43)


---

<a id="step-43"></a>

### Step 43: Understanding python -m

**Why we are doing this.** We paused to understand why we invoke package installers through `python -m` instead of relying on whichever `pip` command happens to be first on PATH.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; Runs the pip command directly.*

```powershell
python -m pip install torch
```

**How it works.** `-m` asks the selected Python executable to run a package module. That reduces the chance of installing dependencies into the wrong interpreter.

**How we checked it.** This was a concept explanation rather than a separate package installation.

**Connects to.** Previous: [Step 42 — Installing PyTorch](06-ai-forecasting-feasibility-test.md#step-42) · Next: [Step 44 — Installing AI Dependencies](06-ai-forecasting-feasibility-test.md#step-44)


---

<a id="step-44"></a>

### Step 44: Installing AI Dependencies

**Why we are doing this.** PyTorch alone was insufficient; the forecasting prototype needed additional model libraries.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt
```

**How it works.** The install command adds the remaining AI dependencies. Keeping these in `.venv` makes experimentation reversible and isolated.

**How we checked it.** The source includes follow-up dependency checks and subsequent attempts to run the models.

**Connects to.** Previous: [Step 43 — Understanding python -m](06-ai-forecasting-feasibility-test.md#step-43) · Next: [Step 45 — Documenting Model Results](06-ai-forecasting-feasibility-test.md#step-45)


---

<a id="step-45"></a>

### Step 45: Documenting Model Results

**Why we are doing this.** The experimentation produced findings worth keeping even if a model proved too costly or unsuitable for the laptop.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

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

*2. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

*3. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal commit -m "docs: record AI forecasting feasibility results"
```

*4. Recorded snippet.*

```powershell
git -C C:\Users\YOUR_USERNAME\projects2026\inventory-development-journal push
```

**How it works.** We recorded model feasibility observations and the conditions of the tests. An exploratory run does not establish a deployed forecasting service.

**How we checked it.** The source describes model outcomes and checks, but the journal should distinguish those observations from future model selection.

**Connects to.** Previous: [Step 44 — Installing AI Dependencies](06-ai-forecasting-feasibility-test.md#step-44) · Next: [Step 46 — Update Module 0 Report](07-module-0-completion.md#step-46)

---

**Chapter recap.** Understand the role of PyTorch, isolated packages, and feasibility results; recognize that no forecasting endpoint was deployed yet.

**Practice explaining:** Distinguish a feasibility test from a validated production model.

