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

## Step 6: Create .env

**Python was already installed on your laptop. We did not download or reinstall Python.** We created a separate environment that uses your existing Python 3.12.12, and then installed Django inside that environment.

I'll explain exactly what happened.

#### 1. What we did, step by step

    **Step 1 — Checked installed Python versions**

    ```powershell
    py --list
    
```

    This showed that your laptop already had Python 3.14 and Python 3.12.12.

    We chose 3.12.12 because it is more compatible with the AI packages we intend to use.

    **Step 2 — Created a virtual environment**

    ```powershell
    py -V:Astral/CPython3.12.12 -m venv .venv
    
```

    Breaking down the command:

    | Part | Meaning |
    |---|---|
    | `py` | Windows Python launcher |
    | `-V:Astral/CPython3.12.12` | Select your existing Python 3.12.12 |
    | `-m venv` | Run Python's virtual environment creator |
    | `.venv` | Name of the folder to create |

    This created the `.venv` folder. It contains a Python executable linked to the existing installation and an isolated location for project packages.

    **Step 3 — Verified the environment**

    ```powershell
    .\.venv\Scripts\python.exe --version
    
```

    This means: run the Python executable inside `.venv` and print its version.

    Your output was `Python 3.12.12`, confirming that it works.

    **Step 4 — Installed Django**

    ```powershell
    .\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
    
```

    | Part | Meaning |
    |---|---|
    | `.\.venv\Scripts\python.exe` | Use our project's Python |
    | `-m pip` | Run Python's package installer |
    | `install` | Install packages |
    | `-r` | Read package names from a file |
    | `requirements.txt` | File listing the required packages |

    This is the command that **installed Django 6.0.9 inside `.venv`**, along with its dependencies.

#### 2. What does the setup look like now?

    Your laptop

    Existing Python installation
    Python 3.12.12 — already installed

    .venv — your project's environment

    Python 3.12
    Environment executable

    Django 6.0.9
    Installed with pip

Think of `.venv` as a dedicated workspace for your inventory project. It uses the Python you already have, but keeps this project's installed libraries separate from other projects.

#### 3. What is the next step?

We've installed the necessary packages. Now we need to tell Django how to configure the application.

Django needs settings such as the database type and a secret key. We keep these in a file named `.env`.

Your project currently contains `.env.example`, which is a template.

#### Step 5 — Create `.env`

Run this in your current PowerShell terminal:

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

This command means:

- `Copy-Item`: Make a copy of a file.
- First path: The existing `.env.example` file.
- Second path: The new `.env` file.

**Nothing is installed by this command.** It simply copies the configuration template.

After running it, you should see `.env` inside the project folder alongside `.env.example`. The new file may be hidden by VS Code's Explorer settings.

Stop there and tell me when it's done. We'll configure the secret key next, then create the SQLite database.

#### How we checked it

#### Step 1 — Check the virtual environment after moving

1. Open the project from its **new location** in VS Code.
2. Open **Terminal → New Terminal**.
3. Make sure you're in the outer project folder containing `.venv`.
4. Run:

```powershell
.\.venv\Scripts\python.exe -m django --version
```

**What this command does:**
- Uses Python from your project's `.venv`.
- Checks whether Django is still accessible after moving the folder.
- Displays the installed Django version.

**Expected output:**
```text
6.0.9
```

Send me the output. If it works, we'll continue with the `.env` configuration. If not, we'll repair the virtual environment without affecting your project files.

#### How we checked it

**Django 6.0.9 is working after the move.** You don't need to recreate the virtual environment or reinstall Django.

The `(.venv)` at the beginning of your terminal also indicates that your virtual environment is activated.

#### One important observation

Your project is now inside `OneDrive\Desktop\my-projects`.

Because we're using SQLite, I recommend keeping the project outside OneDrive to avoid possible database synchronization and file-locking problems. A suitable location would be `C:\Users\mehre\my-projects`.

#### Next step — Check the `.env` file

Once the project is in its final location, run:

```powershell
Test-Path .\techmindz_inventory_module0_sqlite_atomic\.env
```

This checks whether the configuration file we created earlier exists.

- `True` — The file exists; we can configure the secret key.
- `False` — We need to create it.

**No changes are made by this command.** Send me the result, and we'll continue.

#### How we checked it

**Your project is correctly set up in its new location.** All three checks passed.

| Check | Result |
|---|---|
| Virtual environment activated |  |
| Django 6.0.9 working |  |
| `.env` file exists |  |

We don't need to reinstall anything.

#### Next step — Configure Django's secret key

Django uses a **secret key** for security-related operations, such as signing session data. Each project should have its own unpredictable key.

#### 1. Generate a secret key

Run:

```powershell
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

This asks Python to generate a random secret key using Django's built-in function.

#### 2. Save it in `.env`

Open the `.env` file inside `techmindz_inventory_module0_sqlite_atomic`.

Find:

```env
DJANGO_SECRET_KEY=replace-with-random-secret
```

Replace `replace-with-random-secret` with the generated key, keeping `DJANGO_SECRET_KEY=` unchanged.

Save the file with `Ctrl + S`.

**Do not share your secret key here.** Once you've saved it, tell me **"Done"**.

Next, we'll initialize the SQLite database and run Django's tests.

#### How we checked it

The function is **`get_random_secret_key()`**.

It's a function provided by Django that generates a random, unpredictable string to use as your project's secret key.

Here's the command:

```powershell
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Breaking it down:

| Code | Meaning |
|---|---|
| `from django.core.management.utils import get_random_secret_key` | Import the function from Django |
| `get_random_secret_key()` | Call the function to generate a new secret key |
| `print(...)` | Display the generated key |
| `python -c` | Tell Python to execute the code inside the quotation marks |

**Why do we need it?** Django uses the secret key to cryptographically sign certain data, helping detect tampering.

The function only generates and displays the key. It does **not** automatically save it to `.env`. You'll copy it there yourself.

Keep the generated key private.

#### How we checked it

#### Next step — Check Django's configuration

Assuming you've saved the generated secret key in `.env`, run this in your VS Code terminal:

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py check
```

**What this does:**
- `manage.py` is Django's project management script.
- `check` checks the project configuration for common errors.
- It does not modify your database or project files.

**Expected output:**

```text
System check identified no issues (0 silenced).
```

Send me the output. If it passes, we'll proceed to creating the SQLite database.

#### How we checked it

We can integrate **GitHub into our development process from now on**, so your project is backed up and its progress is documented.

Our workflow will be:

    **Write or modify code**

    **Run tests and verify**

    **Git commit → Push to GitHub**

  Proceed to the next module

We'll use one GitHub repository for the entire inventory project, with meaningful commits after each completed development stage.

**Important:** We'll ensure `.env`, `.venv`, SQLite database files, and other local/generated files are excluded before our first commit. Your Django secret key must never be uploaded.

#### Step 1 — Check whether Git is installed

In your existing VS Code terminal, run:

```powershell
git --version
```

This checks whether Git is available on your computer.

Send me the output. We'll then initialize Git, configure `.gitignore`, create a GitHub repository, and make our first push—one step at a time.

#### How we checked it

Here's a sample entry based on a step we actually completed.

#### Entry 07 — Verifying Django After Moving the Project

**Date:** 3 November 2026
**Module:** 0 — Project Setup
**Status:** Completed

#### 1. Objective
Verify that Django still works after moving the project to its permanent location.

#### 2. Command executed
```powershell
.\.venv\Scripts\python.exe -m django --version
```

#### 3. Understanding the command
- `.\.venv\Scripts\python.exe` — Uses Python from our project's virtual environment.
- `-m django` — Runs Django as a Python module.
- `--version` — Displays the installed Django version.

#### 4. Actual result
```text
6.0.9
```

**Outcome:** Django was successfully accessed from the virtual environment after relocation.

#### 5. What I learned
- A virtual environment keeps project dependencies isolated.
- Moving a project can sometimes break virtual environment paths.
- Running a verification command helps confirm that the environment still works.

#### 6. Problems encountered
No errors during this verification.

#### 7. Git commit
Pending — GitHub setup has not yet been completed.

#### 8. Revision question
**Q:** Why did we check Django after moving the project?

**A:** Because virtual environments can contain paths linked to their original location. We needed to verify that Django remained accessible.

I'd recommend keeping every entry at roughly this level of detail: **what we did, why, the actual result, and what you learned**. For coding stages, we'll also record which files changed and which tests passed.

This way, the journal becomes useful for both revision and explaining your project during interviews.

#### How we checked it

Yes. We'll maintain **both GitHub repositories side by side** throughout development.

| Public repository | Private repository |
|---|---|
| Django source code | Step-by-step learning journal |
| Features and functionality | Commands and explanations |
| Tests and bug fixes | Errors encountered and solutions |
| Project documentation | Concepts and revision questions |
| Commit after verified changes | Update after each completed step |

#### Our development workflow

1. **Learn** — Understand what we're about to implement.
2. **Implement** — Write the code.
3. **Test** — Verify that it works.
4. **Document** — Record what we did, why, and the results in the private journal.
5. **Push** — Commit and push the code to the public repository and the journal to the private repository.

We'll maintain separate Git repositories so the private journal never accidentally becomes part of the public project's commit history.

#### Next step

Let's begin setting up Git.

Run this in your VS Code terminal:

```powershell
git --version
```

Send me the output. We'll proceed one step at a time, explaining each command as we go.

#### How we checked it

**Git 2.54.0 is installed and working.**

We'll set up two separate repositories:

- **Public:** Inventory system source code.
- **Private:** Development journal and revision notes.

#### Step 2 — Check for `.gitignore`

Before uploading anything, we need to protect files such as `.env`, which contains your Django secret key.

Run:

```powershell
Test-Path .\techmindz_inventory_module0_sqlite_atomic\.gitignore
```

**What is `.gitignore`?** It's a file that tells Git which files and folders it should not track.

**What this command does:** Checks whether `.gitignore` already exists in your Django project folder.

- `True` — We'll inspect its contents.
- `False` — We'll create it.

Send me the result. We won't make the first commit until we've checked that sensitive files are excluded.

#### How we checked it

**The `.gitignore` file exists.** Next, we'll check whether it excludes sensitive and unnecessary files.

#### Step 3 — Inspect `.gitignore`

Run:

```powershell
Get-Content .\techmindz_inventory_module0_sqlite_atomic\.gitignore
```

**What this command does:** Displays the contents of `.gitignore` without modifying anything.

We'll check whether it excludes `.env`, `.venv`, SQLite database files, and Python cache files.

Send me the output. We'll verify it before initializing the public Git repository.

#### How we checked it

**Your `.gitignore` looks correct.** It excludes the important files:

- `.env` — Django secret key and configuration
- `.venv/` — Virtual environment
- `*.sqlite3` — Local database files
- `__pycache__/` — Python-generated cache
- `.model_cache/` — Downloaded AI model cache

We'll use `techmindz_inventory_module0_sqlite_atomic` as the **public repository root**. The private learning journal will live in a separate repository.

#### Step 4 — Check whether Git is already initialized

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status
```

**Understanding the command:**

- `git` — Runs Git.
- `-C` — Tells Git to work inside the specified folder.
- `status` — Shows the repository's current state.

This command **does not modify anything**.

Send me the output. Then we'll initialize the repository if necessary, before making our first commit.

#### How we checked it

We'll name the **public GitHub repository**:

`ai-assisted-inventory-manager`

You don't need to rename your existing project folder. The GitHub repository name and local folder name can be different.

The private development journal will have its own repository, which we'll set up separately.

#### Next step — Check Git status

Run:

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status
```

This checks whether Git has already been initialized in your Django project folder.

Send me the output, and we'll continue setting up `ai-assisted-inventory-manager`.

*Previous: Step 5 (Change manage.py)  |  Next: Step 7 (Initialize the Git repository)*

---
