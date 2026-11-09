# MODULE 0 — Foundations

## Chapter 6: AI forecasting feasibility test (Steps 42–45)

### Before we begin

The eventual inventory website is meant to forecast demand, but we had not yet built that part of the website.

### What we worked on

We prepared the machine-learning libraries and tried early experiments to see whether forecasting tools could run on this computer.

### Picture it this way

A small experiment is not the same as a finished forecasting feature. These notes tell us what we explored, not that customers can already see predictions on a dashboard.

### Find a step

- [Step 42: Installing PyTorch](#step-42)
- [Step 43: Understanding python -m](#step-43)
- [Step 44: Installing AI Dependencies](#step-44)
- [Step 45: Documenting Model Results](#step-45)

---

<a id="step-42"></a>

### Step 42: Installing PyTorch

#### Before this step

The forecasting experiment needed a library called PyTorch, but it was not installed yet. The preceding step, Step 41, covered record and push test results, so this was the next part of the development sequence.

#### What we did and why

PyTorch provides functions used by many machine-learning models. Installing it gives us a tool for experiments; it does not train a model or guarantee that predictions will be good.

#### Code and commands

**Step 42, block 1.** This code sample or terminal command was used while working on installing pytorch. The content below remains in its original sequence for Step 42, block 1.

```powershell
python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 42, installing pytorch involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu` starts Python or one of its tools from the development terminal.

#### How we checked it

For Step 42 (Installing PyTorch), we compared the visible terminal output with the requested action. The visible result for Step 42 was described as “successfully installed”; this only addressed the installing pytorch check in `VS Code terminal / project setup`.

#### Next

[Step 41: Record and Push Test Results](../Module-0/05-security-checks.md#step-41) · [Step 43: Understanding python -m](#step-43)


---

<a id="step-43"></a>

### Step 43: Understanding python -m

#### Before this step

We used commands such as python -m pip, and wanted to know why the word python was included. Immediately before this, Step 42 dealt with installing pytorch; the work here continued the same part of the application.

#### What we did and why

pip installs Python packages. The command python -m pip explicitly asks the current Python installation to run its own installer, reducing the chance that packages go into the wrong Python environment.

#### Code and commands

**Step 43, block 1.** This code sample or terminal command was used while working on understanding python -m. The content below remains in its original sequence for Step 43, block 1.

```powershell
pip install torch
```

**Step 43, block 2.** This code sample or terminal command was used while working on understanding python -m. The content below remains in its original sequence for Step 43, block 2.

```powershell
python -m pip install torch
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 43, understanding python -m involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `pip install torch` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 43, understanding python -m involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -m pip install torch` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 43. For Step 43, the proposed way to check understanding python -m was to revisit `VS Code terminal / project setup` and run the corresponding command or feature to see its result. Development continued in Step 44 before a complete result could be claimed.

#### Next

[Step 42: Installing PyTorch](#step-42) · [Step 44: Installing AI Dependencies](#step-44)


---

<a id="step-44"></a>

### Step 44: Installing AI Dependencies

#### Before this step

The experiment required additional ready-made libraries beyond PyTorch. Immediately before this, Step 43 dealt with understanding python -m; the work here continued the same part of the application.

#### What we did and why

The AI requirements file listed those extra packages. Installing them prepared the testing environment, but we still had to run experiments and inspect results before deciding what approach was suitable.

#### Code and commands

**Step 44, block 1.** This code sample or terminal command was used while working on installing ai dependencies. The content below remains in its original sequence for Step 44, block 1.

```powershell
python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt
```

**Step 44, block 2.** This code sample or terminal command was used while working on installing ai dependencies. The content below remains in its original sequence for Step 44, block 2.

```powershell
python -c "import torch, chronos, psutil; print('Torch:', torch.__version__)"
```

**Step 44, block 3.** This output was used while working on installing ai dependencies. The content below remains in its original sequence for Step 44, block 3.

```text
Torch: 2.10.0+cpu
```

**Step 44, block 4.** This code sample or terminal command was used while working on installing ai dependencies. The content below remains in its original sequence for Step 44, block 4.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py --model bolt
```

**Step 44, block 5.** This code sample or terminal command was used while working on installing ai dependencies. The content below remains in its original sequence for Step 44, block 5.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 44, installing ai dependencies involved `requirements-ai.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 44, installing ai dependencies involved `requirements-ai.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -c "import torch, chronos, psutil; print('Torch:', torch.__version__)"` starts Python or one of its tools from the development terminal.

**Block 3 (text, 1 lines).** In Step 44, installing ai dependencies involved `requirements-ai.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 44, output block 3 showed the result rather than another command. Its visible value was `Torch: 2.10.0+cpu`, which mattered for installing ai dependencies.

**Block 4 (powershell, 1 lines).** In Step 44, installing ai dependencies involved `requirements-ai.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py --model bolt` starts Python or one of its tools from the development terminal.

**Block 5 (powershell, 1 lines).** In Step 44, installing ai dependencies involved `requirements-ai.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py` starts Python or one of its tools from the development terminal.

#### How we checked it

For Step 44 (Installing AI Dependencies), we compared the visible terminal output with the requested action. The visible result for Step 44 was described as “successfully installed”; this only addressed the installing ai dependencies check in `requirements-ai.txt`.

#### Next

[Step 43: Understanding python -m](#step-43) · [Step 45: Documenting Model Results](#step-45)


---

<a id="step-45"></a>

### Step 45: Documenting Model Results

#### Before this step

After running early model tests, we wanted to record the findings instead of relying on memory. Immediately before this, Step 44 dealt with installing ai dependencies; the work here continued the same part of the application.

#### What we did and why

A feasibility result answers “can we run this kind of tool here?” It does not yet answer “does our finished inventory website make reliable predictions?” We kept that difference clear in the written explanation.

#### Code and commands

**Step 45, block 1.** This code sample or terminal command was used while working on documenting model results. The content below remains in its original sequence for Step 45, block 1.

```powershell
Add-Content -Path "C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal\inventory-development-journal.md" -Value @'

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

**Step 45, block 2.** This code sample or terminal command was used while working on documenting model results. The content below remains in its original sequence for Step 45, block 2.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inventory-development-journal.md
```

**Step 45, block 3.** This code sample or terminal command was used while working on documenting model results. The content below remains in its original sequence for Step 45, block 3.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m "docs: record AI forecasting feasibility results"
```

**Step 45, block 4.** This code sample or terminal command was used while working on documenting model results. The content below remains in its original sequence for Step 45, block 4.

```powershell
git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push
```

**Step 45, block 5.** This code sample or terminal command was used while working on documenting model results. The content below remains in its original sequence for Step 45, block 5.

```powershell
code .\techmindz_inventory_module0_sqlite_atomic\MODULE0_REPORT.md
```

#### How the code works

**Block 1 (powershell, 65 lines).** In Step 45, documenting model results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The comment `## Module 0 — AI Forecasting Feasibility Test` explains the intention of the following section; it does not execute as application logic. The comment `### Objective` explains the intention of the following section; it does not execute as application logic.

**Lines 8–14:** The comment `### Models` explains the intention of the following section; it does not execute as application logic. The comment `### Libraries` explains the intention of the following section; it does not execute as application logic.

**Lines 15–21:** The comment `### Commands` explains the intention of the following section; it does not execute as application logic. The command `python -m pip install torch==2.10.0 --index-url https://download.pytorch.org/whl/cpu` starts Python or one of its tools from the development terminal. The command `python -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements-ai.txt` starts Python or one of its tools from the development terminal. The command `python -c "import torch, chronos, psutil; print('Torch:', torch.__version__)"` starts Python or one of its tools from the development terminal.

**Lines 22–28:** The command `python .\techmindz_inventory_module0_sqlite_atomic\scripts\ai_probe.py` starts Python or one of its tools from the development terminal. The comment `### Test Data` explains the intention of the following section; it does not execute as application logic.

**Lines 29–35:** The comment `### Results — Both-Model Run` explains the intention of the following section; it does not execute as application logic.

**Lines 36–42:** The lines starting with `- Forecast computation: 0.01 seconds` supply the values or page content for this part of inventory-development-journal.md. These lines are kept together because their order and indentation matter.

**Lines 43–49:** The comment `### Interpretation` explains the intention of the following section; it does not execute as application logic.

**Lines 50–56:** The comment `### Issues` explains the intention of the following section; it does not execute as application logic.

**Lines 57–63:** The comment `### Next Steps` explains the intention of the following section; it does not execute as application logic. The comment `### Status` explains the intention of the following section; it does not execute as application logic.

**Lines 64–65:** The lines starting with `Model selection remains pending.` supply the values or page content for this part of inventory-development-journal.md. These lines are kept together because their order and indentation matter.

**Block 2 (powershell, 1 lines).** In Step 45, documenting model results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal add inven` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 3 (powershell, 1 lines).** In Step 45, documenting model results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal commit -m` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 4 (powershell, 1 lines).** In Step 45, documenting model results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C C:\Users\YOUR_WINDOWS_USERNAME\projects2026\inventory-development-journal push` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 5 (powershell, 1 lines).** In Step 45, documenting model results involved `inventory-development-journal.md`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `code .\techmindz_inventory_module0_sqlite_atomic\MODULE0_REPORT.md` supply the values or page content for this part of inventory-development-journal.md. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 45. For Step 45, the proposed way to check documenting model results was to run the appropriate Django migration or model check and inspect its result in the terminal. Step 45 did not establish a successful outcome for documenting model results.

#### Next

[Step 44: Installing AI Dependencies](#step-44) · [Step 46: Update Module 0 Report](../Module-0/07-module-0-completion.md#step-46)


### Check your understanding

Without looking at the code, explain why **ai forecasting feasibility test** was needed at this point, and describe what the application did differently after the final step of this chapter.
