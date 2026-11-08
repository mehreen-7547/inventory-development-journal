# MODULE 0 — Getting started

## Chapter 1: Environment setup (Steps 1–6)

### Before we begin

We had a Django starter project, but we had not yet made sure the laptop could run it.

### What we worked on

Before building an inventory screen, we checked Python, created a separate project environment, installed the required packages and prepared local settings.

### Picture it this way

Think of this as setting up the desk and tools before starting a piece of work. At the end, we know where the main Django folder is and which Python runs it.

> **How to use these notes:** This chapter tells the story of how the project was built. It is not asking you to run every old command again. Some steps show a first attempt that is corrected later.

### Jump to a step

- [Step 1: Check Python](#step-1)
- [Step 2: Create the virtual environment](#step-2)
- [Step 3: Install Django and required packages](#step-3)
- [Step 4: Create the configuration file](#step-4)
- [Step 5: Understand the Django project folders](#step-5)
- [Step 6: Create .env](#step-6)

---

<a id="step-1"></a>

### Step 1: Check Python

**What was happening before this step?**

Before we could build a website, we needed to know which version of Python was installed on the laptop. Python is the program that runs Django, so this check told us whether we could run the project at all.

**What did we do, and what does it mean?**

The command only displays a version number. It does not install or change Python. Because the first version we tried was missing, we checked which versions were already available instead of guessing.

**Code or command from this step**

*1. Run.*

```powershell
py -3.12 --version
```

*2. Code or command used at this point.*

```powershell
py -3.13 --version
```

**How did we know what happened?**

The next recorded check showed Python 3.13 was unavailable, so the installed-version list was checked before continuing.

**Where to go next:** Next: [Step 2 — Create the virtual environment](01-environment-setup.md#step-2)


---

<a id="step-2"></a>

### Step 2: Create the virtual environment

**What was happening before this step?**

We had found a usable version of Python. The next problem was keeping this project's tools separate from the tools used by other Python projects.

**What did we do, and what does it mean?**

A virtual environment is simply a private folder for this project's Python and installed packages. The command creates `.venv`, and the second command checks that the Python inside that folder is the one we intended to use.

**Code or command from this step**

*1. In VS Code, open the folder containing manage.py, then run.*

```powershell
py -V:Astral/CPython3.12.12 -m venv .venv
```

*2. Code or command used at this point.*

```powershell
.\.venv\Scripts\python.exe --version
```

*3. Example result from the notes (not a command to run).*

```text
Python 3.12.12
```

**Understanding the code, one piece at a time**

The unusual part of the first command is `-m venv .venv`: it asks Python to create a new environment folder named `.venv`. The next command uses the Python program inside that folder. This checks that later installations will go into the right place.

**How did we know what happened?**

The notes confirm the environment was created; the version command was used to verify Python 3.12.12.

**Where to go next:** Previous: [Step 1 — Check Python](01-environment-setup.md#step-1) · Next: [Step 3 — Install Django and required packages](01-environment-setup.md#step-3)


---

<a id="step-3"></a>

### Step 3: Install Django and required packages

**What was happening before this step?**

The private Python environment was ready, but it did not yet have the packages needed to run the starter project.

**What did we do, and what does it mean?**

A package is ready-made Python code we install instead of writing everything ourselves. The requirements file lists the packages this project needs. Installing them here is like gathering the tools before beginning construction; we have not built any application features yet.

**Code or command from this step**

*1. Run this in the same terminal.*

```powershell
.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
```

**How did we know what happened?**

The following recorded step says Django 6.0.9 and the required packages installed successfully after a temporary connection retry.

**Where to go next:** Previous: [Step 2 — Create the virtual environment](01-environment-setup.md#step-2) · Next: [Step 4 — Create the configuration file](01-environment-setup.md#step-4)


---

<a id="step-4"></a>

### Step 4: Create the configuration file

**What was happening before this step?**

Our program will need some values that can differ between computers, such as local settings and private keys. We did not want to hard-code those values into files shared on GitHub.

**What did we do, and what does it mean?**

The example configuration file is a blank template with the right variable names. Copying it creates a local working file. The example can be shared; the working copy may contain private information and should not be uploaded.

**Code or command from this step**

*1. Run this command in the same terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How did we know what happened?**

Our recorded notes indicate the virtual environment was working, but did not provide an independent test of the copied configuration at this point.

**Where to go next:** Previous: [Step 3 — Install Django and required packages](01-environment-setup.md#step-3) · Next: [Step 5 — Understand the Django project folders](01-environment-setup.md#step-5)


---

<a id="step-5"></a>

### Step 5: Understand the Django project folders

**What was happening before this step?**

The downloaded starter project had several folders, so it was easy to open the wrong one or run a command in the wrong place.

**What did we do, and what does it mean?**

`manage.py` is the file Django uses to start its commands. By locating it first, we know where to run commands such as starting the server or creating database tables. This step mainly clarified the project layout, rather than adding a new feature.

**Code or command from this step**

*1. You can continue with this command in your current terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How did we know what happened?**

This was mainly a folder-structure explanation. No separate code test was documented in this step.

**Where to go next:** Previous: [Step 4 — Create the configuration file](01-environment-setup.md#step-4) · Next: [Step 6 — Create .env](01-environment-setup.md#step-6)


---

<a id="step-6"></a>

### Step 6: Create .env

**What was happening before this step?**

Django now had its packages, but it still needed local settings so it could start safely on this computer.

**What did we do, and what does it mean?**

A secret key is a private value Django uses for security-related features. We put local values in `.env` instead of inside publicly shared code. The setup checks here were meant to show that these settings were available; they did not mean the full inventory system was finished.

**Code or command from this step**

*1. Code or command used at this point.*

```powershell
    py --list
    
```

*2. Step 2 — Created a virtual environment.*

```powershell
    py -V:Astral/CPython3.12.12 -m venv .venv
    
```

*3. Code or command used at this point.*

```powershell
    .\.venv\Scripts\python.exe --version
    
```

*4. Code or command used at this point.*

```powershell
    .\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
    
```

*5. Run this in your current PowerShell terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How did we know what happened?**

Later notes record successful Django system checks. This step contains several exploratory checks, not one single completed operation.

**Where to go next:** Previous: [Step 5 — Understand the Django project folders](01-environment-setup.md#step-5) · Next: [Step 7 — Initialize the Git repository](02-git-and-github.md#step-7)

---

### The point of this chapter

Think of this as setting up the desk and tools before starting a piece of work. At the end, we know where the main Django folder is and which Python runs it.

**Interview practice:** Close the code and explain the chapter in your own words: what problem existed, which file handled it, and what happened when someone used the feature.

