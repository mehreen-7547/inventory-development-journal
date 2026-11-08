# Module 0, Chapter 1: Environment setup

## Step 1: Check Python

#### Development begins — Module 0

We'll proceed **one step at a time**, following the development guide. Each module must pass its tests before we move to the next.

  **[Download Module 0 Starter](sandbox:/mnt/data/Techmindz_Inventory_Module0_SQLite_Atomic_Update.zip)**

  Includes Django, SQLite configuration, stock-safety tests and the AI model testing script.

#### Step 1 — Check Python

1. Extract the ZIP to a folder on your laptop.
2. Open that folder in VS Code.
3. Open **Terminal → New Terminal**.
4. Run:

```powershell
py -3.12 --version
```

**Expected output:** `Python 3.12.x`

If Python 3.12 is unavailable, try:

```powershell
py -3.13 --version
```

Send me the output. Next, we'll create the virtual environment and install Django.

**Development rules:** SQLite for now, atomic conditional stock updates, no paid services, and no changes to agreed requirements without your approval.

#### How we checked it

This means **Python 3.13 is not installed or detected** on your laptop.

#### Step 1 — Check available Python versions

Run this in the same PowerShell terminal:

```powershell
py --list
```

This will show which Python versions are installed.

**Send me the output.** We'll use Python 3.12 if available; otherwise, we'll install it before proceeding.

*Next: Step 2 (Create the virtual environment)*

---

## Step 2: Create the virtual environment

**Python 3.12.12 is already installed**, through Astral. No additional installation is needed.

#### Step 2 — Create the virtual environment

In VS Code, open the folder containing `manage.py`, then run:

```powershell
py -V:Astral/CPython3.12.12 -m venv .venv
```

Once it finishes, verify the Python version:

```powershell
.\.venv\Scripts\python.exe --version
```

**Expected output:**
```text
Python 3.12.12
```

Send me the output. Next, we'll install the Django dependencies.

#### How we checked it

We just created a **Python virtual environment** called `.venv` for your inventory project.

#### What does that mean?

A virtual environment is an isolated space where we install the Python packages needed for this project.

For example:

- Your computer has Python 3.14 as its default.
- We created `.venv` using **Python 3.12.12**, which is more suitable for our AI dependencies.
- Django, PyTorch and other packages will be installed inside `.venv` without interfering with your other Python projects.

In your screenshot, the `.venv` folder contains `Include`, `Lib`, `Scripts` and `pyvenv.cfg`. This indicates the environment was created successfully.

**We haven't installed Django or run the project yet.**

#### Next step — Verify Python

In the same terminal, run:

```powershell
.\.venv\Scripts\python.exe --version
```

Expected:

```text
Python 3.12.12
```

Send me the output. Then we'll install the required packages.

*Previous: Step 1 (Check Python)  |  Next: Step 3 (Install Django and required packages)*

---

## Step 3: Install Django and required packages

**Python 3.12.12 is confirmed.** Your virtual environment is working correctly.

#### Step 3 — Install Django and required packages

Run this in the same terminal:

```powershell
.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
```

**What this does:** Installs the project's required Python packages inside `.venv`, without affecting your other projects.

Wait until the installation finishes, then send me the last few lines of the terminal output.

Next, we'll configure Django and SQLite.

*Previous: Step 2 (Create the virtual environment)  |  Next: Step 4 (Create the configuration file)*

---

## Step 4: Create the configuration file

**Step 3 completed successfully.**

Django 6.0.9 and all required packages are installed in your Python 3.12 virtual environment.

The internet connection timed out briefly, but pip retried and completed the installation. The pip update notice is optional.

We can now configure Django to use SQLite.

#### Step 4 — Create the configuration file

Run this command in the same terminal:

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**What this does:** Creates a `.env` file for private configuration, including the Django secret key and database settings.

The database is already configured as SQLite.

Run the command and tell me whether it completes without errors. Next, we'll generate the secret key.

#### How we checked it

The `.venv` folder in your screenshot is **correctly created and working**.

It contains your project's isolated Python 3.12 environment, including the Django packages we just installed.

#### Do we need to activate it?

**Not necessarily.** We've been using:

```powershell
.\.venv\Scripts\python.exe
```

This directly uses the virtual environment, even without activating it.

#### Configure VS Code to use it

This will also help VS Code recognize Django imports.

1. Press `Ctrl + Shift + P`.
2. Search for **Python: Select Interpreter**.
3. Select `.venv\Scripts\python.exe` (Python 3.12.12).

Your `.venv` is currently in the outer folder, while `manage.py` is inside the project subfolder. **That's fine. No need to move or recreate anything.**

After selecting the interpreter, we can continue configuring `.env`.

*Previous: Step 3 (Install Django and required packages)  |  Next: Step 5 (Change manage.py)*

---
