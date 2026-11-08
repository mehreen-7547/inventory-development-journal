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

## Step 5: Change manage.py

#### Module 0 — What we've done so far

We're setting up the foundation for your **AI Inventory & Demand Intelligence System**. We haven't started building the actual inventory features yet.

#### Our progress

    **Environment setup**
    3 of 6 steps completed

  {#each [
    {name:"Downloaded and opened the Django starter",detail:"The project files are available in VS Code.",done:true},
    {name:"Selected Python 3.12.12",detail:"Your computer also has Python 3.14, but we're using 3.12 for better AI package compatibility.",done:true},
    {name:"Created .venv and installed packages",detail:"Django 6.0.9 and its dependencies installed successfully.",done:true},
    {name:"Configure .env",detail:"Copy the example configuration and set a Django secret key.",done:false},
    {name:"Initialize SQLite and run Django tests",detail:"Create database tables and test stock-safety logic.",done:false},
    {name:"Install and test AI models",detail:"Measure whether Chronos-Bolt Tiny and Chronos-2 run on your laptop.",done:false}
  ] as s,i}

        {s.name}
        {s.detail}

  {/each}

#### Understanding what we installed

    Your laptop

    Installed Python versions
    Python 3.14 and Python 3.12.12

    .venv — Project environment

    Python
    3.12.12

    Django
    6.0.9

  The virtual environment keeps your project's Python packages separate from other projects. We can install more packages here without changing your system-wide Python setup.

#### Your current folder structure

```text
Techmindz_Inventory_Module0_SQLite_Atomic_Update/
│
├── .venv/                 ← Python environment
│
└── techmindz_inventory_module0_sqlite_atomic/
    ├── config/            ← Django configuration
    ├── core/              ← Initial Django app
    ├── scripts/           ← AI testing scripts
    ├── manage.py          ← Django command entry point
    ├── requirements.txt   ← Python dependencies
    └── .env.example       ← Configuration template
```

#### What comes next?

**Step 4: Create the `.env` file.** This will tell Django which database to use and where to read its private settings.

You can continue with this command in your current terminal:

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

After that, we'll generate a Django secret key, create the SQLite database and run the initial tests.

**Current status:** Python and Django are ready. The application and AI models have not been run yet.

*Previous: Step 4 (Create the configuration file)  |  Next: Step 6 (Create .env)*

---
