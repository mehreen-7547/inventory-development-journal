# MODULE 0 — Foundations

## Chapter 1: Environment setup (Steps 1–6)

**Where we are.** Before writing inventory features, we made sure the laptop could run the Django starter project reliably.

**What this chapter gives us.** Python 3.12 in a virtual environment, installed dependencies, and local configuration separate from Git.

**How to read this chapter.** These are retrospective notes of what was done, not a list of commands to run again on your current project. Some early versions are intentionally shown before later fixes. The original raw records remain on GitHub `main`.

**Interview checkpoint.** Explain why a virtual environment, manage.py, requirements.txt, and .env exist.

### Find a step

- [Step 1: Check Python](#step-1)
- [Step 2: Create the virtual environment](#step-2)
- [Step 3: Install Django and required packages](#step-3)
- [Step 4: Create the configuration file](#step-4)
- [Step 5: Understand the Django project folders](#step-5)
- [Step 6: Create .env](#step-6)

---

<a id="step-1"></a>

### Step 1: Check Python

**Why we are doing this.** Before configuring Django, we needed to know which Python installation the laptop could actually run. The project depends on a compatible interpreter.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run.*

```powershell
py -3.12 --version
```

*2. Recorded snippet.*

```powershell
py -3.13 --version
```

**How it works.** The `py` launcher selects an installed Python version; `--version` reports it without changing anything. The notes eventually identify Astral's Python 3.12.12 as the chosen version.

**How we checked it.** The recorded follow-up showed Python 3.13 was unavailable, so the installed-version list was checked before continuing.

**Connects to.** Next: [Step 2 — Create the virtual environment](01-environment-setup.md#step-2)


---

<a id="step-2"></a>

### Step 2: Create the virtual environment

**Why we are doing this.** We had selected Python 3.12, but installing packages globally would interfere with unrelated projects. We therefore created a private environment named `.venv`.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; In VS Code, open the folder containing manage.py, then run.*

```powershell
py -V:Astral/CPython3.12.12 -m venv .venv
```

*2. Recorded snippet.*

```powershell
.\.venv\Scripts\python.exe --version
```

*3. Recorded example/output — **do not type this as an instruction**; Expected output.*

```text
Python 3.12.12
```

**How it works.** The `-m venv` command creates a directory containing a separate Python executable and package directory. Using `.venv\\Scripts\\python.exe` makes later commands use that environment even without activation.

**Follow the flow.** The command starts with the *specific Python installation* chosen for this project. `-m venv` asks that Python to create a separate directory called `.venv`. The executable inside `.venv` is what we use afterwards; it lets us keep the project's package versions separate from other work on the laptop.

**How we checked it.** The notes confirm the environment was created; the version command was used to verify Python 3.12.12.

**Connects to.** Previous: [Step 1 — Check Python](01-environment-setup.md#step-1) · Next: [Step 3 — Install Django and required packages](01-environment-setup.md#step-3)


---

<a id="step-3"></a>

### Step 3: Install Django and required packages

**Why we are doing this.** Once `.venv` existed, Django and the starter project's dependencies had to be installed before the project could run.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this in the same terminal.*

```powershell
.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
```

**How it works.** `python -m pip install -r ...requirements.txt` reads the dependency list and installs it into the selected environment. This step installs dependencies; it does not create database tables.

**How we checked it.** The following recorded step says Django 6.0.9 and the required packages installed successfully after a temporary connection retry.

**Connects to.** Previous: [Step 2 — Create the virtual environment](01-environment-setup.md#step-2) · Next: [Step 4 — Create the configuration file](01-environment-setup.md#step-4)


---

<a id="step-4"></a>

### Step 4: Create the configuration file

**Why we are doing this.** Django settings need environment-specific values without embedding secrets directly in tracked source files. The starter project provided a safe example to copy.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet; Run this command in the same terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How it works.** `Copy-Item` makes a working `.env` from `.env.example`; the real file can contain local settings while the example shows the expected variable names. It should stay outside Git.

**How we checked it.** The source notes indicate the virtual environment was working, but did not provide an independent test of the copied configuration at this point.

**Connects to.** Previous: [Step 3 — Install Django and required packages](01-environment-setup.md#step-3) · Next: [Step 5 — Understand the Django project folders](01-environment-setup.md#step-5)


---

<a id="step-5"></a>

### Step 5: Understand the Django project folders

**Why we are doing this.** The starter project had an outer folder and a Django folder, which made it easy to run commands in the wrong location. We first clarified where `manage.py` and configuration files belonged.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Illustration from the original explanation — **not necessarily an edit**; You can continue with this command in your current terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How it works.** `manage.py` is Django's command entry point. Paths in the notes refer to the inner project folder; understanding that layout prevents `File not found` errors.

**How we checked it.** This was mainly a folder-structure explanation. No separate code test was documented in this step.

**Connects to.** Previous: [Step 4 — Create the configuration file](01-environment-setup.md#step-4) · Next: [Step 6 — Create .env](01-environment-setup.md#step-6)


---

<a id="step-6"></a>

### Step 6: Create .env

**Why we are doing this.** Before running Django, we needed working local configuration, a generated secret key, and clarity about the actual Python environment.

**Original code or command, placed in context.** The snippets below come from the recorded development step. Explanatory examples and expected output are labelled; later steps may supersede this version.

*1. Recorded snippet.*

```powershell
    py --list
    
```

*2. Recorded snippet; Step 2 — Created a virtual environment.*

```powershell
    py -V:Astral/CPython3.12.12 -m venv .venv
    
```

*3. Recorded snippet.*

```powershell
    .\.venv\Scripts\python.exe --version
    
```

*4. Recorded snippet.*

```powershell
    .\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
    
```

*5. Recorded snippet; Run this in your current PowerShell terminal.*

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**How it works.** The notes distinguish the safe `.env.example` from the private `.env`, show how to generate a Django secret key, and describe testing settings from the terminal. An environment variable is read by Django at runtime, not copied into source control.

**Follow the flow.** Think of `.env.example` as a blank form listing settings the project expects. `.env` is the private completed form. Django reads configured values when it starts, and the Django secret key must be generated for the local project. We record the procedure, never the real key.

**How we checked it.** Later notes record successful Django system checks. This step contains several exploratory checks, not one single completed operation.

**Connects to.** Previous: [Step 5 — Understand the Django project folders](01-environment-setup.md#step-5) · Next: [Step 7 — Initialize the Git repository](02-git-and-github.md#step-7)

---

**Chapter recap.** Python 3.12 in a virtual environment, installed dependencies, and local configuration separate from Git.

**Practice explaining:** Explain why a virtual environment, manage.py, requirements.txt, and .env exist.

