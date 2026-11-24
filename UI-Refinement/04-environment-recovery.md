# Troubleshooting: Windows Smart App Control blocks Python virtual environment

Date: 24 November 2026. Encountered during UI refinement Step 543, after pulling source commit d40f25a. This is an environment incident, not a new development step.

## Symptoms and diagnosis
After reopening VS Code, the existing parent-folder .venv (Python 3.12.12) failed with WinError 4551: An Application Control policy has blocked this file. Git initially failed because the terminal was in the parent folder; changing to techmindz_inventory_module0_sqlite_atomic resolved Git. Global Python 3.14.3 and the underlying uv-managed Python 3.12.12 both worked. Direct invocation of the old venv launcher failed, including as administrator. No Zone.Identifier stream was present. Windows Code Integrity events 3118, 3077 and 3033 identified Smart App Control and signing/policy rejection. Smart App Control was On. Creating a test environment with stdlib venv also failed. The old environment had originally been created in Downloads and moved; this does not establish the cause of the sudden policy rejection.

Claude's diagnostic workaround using the base interpreter and PYTHONPATH demonstrated that Django and NumPy packages remained importable. The permanent approach chosen was a new uv-created environment, keeping Windows security enabled.

## Successful recovery
From the parent folder, outside the old environment:
```powershell
py -3.14 -m pip install uv
py -3.14 -m uv venv .venv_new --python 3.12.12
& .\.venv_new\Scripts\python.exe --version
Copy-Item -Path ".venv\Lib\site-packages\*" -Destination ".venv_new\Lib\site-packages" -Recurse -Force
& .\.venv_new\Scripts\Activate.ps1
cd techmindz_inventory_module0_sqlite_atomic
python --version
python -m django --version
python manage.py check
```
Confirmed output: Python 3.12.12; Django 6.0.9; System check identified no issues (0 silenced).

## Follow-up
Select the parent-folder .venv_new/Scripts/python.exe through VS Code Python: Select Interpreter. Keep the old .venv until full regression and AI forecasting checks pass. Copying site-packages is a pragmatic recovery, not a reproducible clean dependency installation; console scripts, editable installs and native dependencies may require further repair. Prefer a requirements-based rebuild if problems arise. No project code or database changes were required. No Windows security setting was disabled. Full tests and browser verification remain pending.

Resume UI Step 543: visually verify the goods receipt form before proceeding.
