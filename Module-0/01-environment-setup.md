# MODULE 0 — Foundations

## Chapter 1: Environment setup (Steps 1–6)

### Before we begin

We had a Django starter project, but we had not yet made sure the laptop could run it.

### What we worked on

Before building an inventory screen, we checked Python, created a separate project environment, installed the required packages and prepared local settings.

### Picture it this way

Think of this as setting up the desk and tools before starting a piece of work. At the end, we know where the main Django folder is and which Python runs it.

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

#### Before this step

Before we could build a website, we needed to know which version of Python was installed on the laptop. Python is the program that runs Django, so this check told us whether we could run the project at all. Before changing application code, we needed a working Python installation so the Django project could run on the laptop.

#### What we did and why

The command only displays a version number. It does not install or change Python. Because the first version we tried was missing, we checked which versions were already available instead of guessing.

#### Code and commands

**Step 1, block 1.** This code sample or terminal command was used while working on check python. The content below remains in its original sequence for Step 1, block 1.

```powershell
py -3.12 --version
```

**Step 1, block 2.** This code sample or terminal command was used while working on check python. The content below remains in its original sequence for Step 1, block 2.

```powershell
py -3.13 --version
```

**Step 1, block 3.** This code sample or terminal command was used while working on check python. The content below remains in its original sequence for Step 1, block 3.

```powershell
py --list
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 1, check python involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `py -3.12 --version` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 1, check python involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `py -3.13 --version` starts Python or one of its tools from the development terminal.

**Block 3 (powershell, 1 lines).** In Step 1, check python involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `py --list` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 1. For Step 1, the proposed way to check check python was to revisit `VS Code terminal / project setup` and run the corresponding command or feature to see its result. Development continued in Step 2 before a complete result could be claimed.

#### Next

[Step 2: Create the virtual environment](#step-2)


---

<a id="step-2"></a>

### Step 2: Create the virtual environment

#### Before this step

We had found a usable version of Python. The next problem was keeping this project's tools separate from the tools used by other Python projects. Immediately before this, Step 1 dealt with check python; the work here continued the same part of the application.

#### What we did and why

A virtual environment is simply a private folder for this project's Python and installed packages. The command creates .venv, and the second command checks that the Python inside that folder is the one we intended to use.

#### Code and commands

**Step 2, block 1.** This code sample or terminal command was used while working on create the virtual environment. The content below remains in its original sequence for Step 2, block 1.

```powershell
py -V:Astral/CPython3.12.12 -m venv .venv
```

**Step 2, block 2.** This code sample or terminal command was used while working on create the virtual environment. The content below remains in its original sequence for Step 2, block 2.

```powershell
.\.venv\Scripts\python.exe --version
```

**Step 2, block 3.** This output was used while working on create the virtual environment. The content below remains in its original sequence for Step 2, block 3.

```text
Python 3.12.12
```

**Step 2, block 4.** This code sample or terminal command was used while working on create the virtual environment. The content below remains in its original sequence for Step 2, block 4.

```powershell
.\.venv\Scripts\python.exe --version
```

**Step 2, block 5.** This output was used while working on create the virtual environment. The content below remains in its original sequence for Step 2, block 5.

```text
Python 3.12.12
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 2, create the virtual environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `py -V:Astral/CPython3.12.12 -m venv .venv` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 1 lines).** In Step 2, create the virtual environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe --version` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

**Block 3 (text, 1 lines).** In Step 2, create the virtual environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 2, output block 3 showed the result rather than another command. Its visible value was `Python 3.12.12`, which mattered for create the virtual environment.

**Block 4 (powershell, 1 lines).** In Step 2, create the virtual environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe --version` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

**Block 5 (text, 1 lines).** In Step 2, create the virtual environment involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 2, output block 5 showed the result rather than another command. Its visible value was `Python 3.12.12`, which mattered for create the virtual environment.

#### How we checked it

Not tested yet at Step 2. For Step 2, the proposed way to check create the virtual environment was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 3 before a complete result could be claimed.

#### Next

[Step 1: Check Python](#step-1) · [Step 3: Install Django and required packages](#step-3)


---

<a id="step-3"></a>

### Step 3: Install Django and required packages

#### Before this step

The private Python environment was ready, but it did not yet have the packages needed to run the starter project. Immediately before this, Step 2 dealt with create the virtual environment; the work here continued the same part of the application.

#### What we did and why

A package is ready-made Python code we install instead of writing everything ourselves. The requirements file lists the packages this project needs. Installing them here is like gathering the tools before beginning construction; we have not built any application features yet.

#### Code and commands

**Step 3, block 1.** This code sample or terminal command was used while working on install django and required packages. The content below remains in its original sequence for Step 3, block 1.

```powershell
.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 3, install django and required packages involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\r` starts Python or one of its tools from the development terminal.

#### How we checked it

Not tested yet at Step 3. For Step 3, the proposed way to check install django and required packages was to revisit `requirements.txt` and run the corresponding command or feature to see its result. Development continued in Step 4 before a complete result could be claimed.

#### Next

[Step 2: Create the virtual environment](#step-2) · [Step 4: Create the configuration file](#step-4)


---

<a id="step-4"></a>

### Step 4: Create the configuration file

#### Before this step

Our program will need some values that can differ between computers, such as local settings and private keys. We did not want to hard-code those values into files shared on GitHub. Immediately before this, Step 3 dealt with install django and required packages; the work here continued the same part of the application.

#### What we did and why

The example configuration file is a blank template with the right variable names. Copying it creates a local working file. The example can be shared; the working copy may contain private information and should not be uploaded.

#### Code and commands

**Step 4, block 1.** This code sample or terminal command was used while working on create the configuration file. The content below remains in its original sequence for Step 4, block 1.

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**Step 4, block 2.** This code sample or terminal command was used while working on create the configuration file. The content below remains in its original sequence for Step 4, block 2.

```powershell
.\.venv\Scripts\python.exe
```

#### How the code works

**Block 1 (powershell, 1 lines).** In Step 4, create the configuration file involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_invent` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

**Block 2 (powershell, 1 lines).** In Step 4, create the configuration file involved `VS Code terminal / project setup`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe` supply the values or page content for this part of VS Code terminal / project setup. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 4. For Step 4, the proposed way to check create the configuration file was to revisit `VS Code terminal / project setup` and run the corresponding command or feature to see its result. Development continued in Step 5 before a complete result could be claimed.

#### Next

[Step 3: Install Django and required packages](#step-3) · [Step 5: Understand the Django project folders](#step-5)


---

<a id="step-5"></a>

### Step 5: Understand the Django project folders

#### Before this step

The downloaded starter project had several folders, so it was easy to open the wrong one or run a command in the wrong place. Immediately before this, Step 4 dealt with create the configuration file; the work here continued the same part of the application.

#### What we did and why

manage.py is the file Django uses to start its commands. By locating it first, we know where to run commands such as starting the server or creating database tables. This step mainly clarified the project layout, rather than adding a new feature.

#### Code and commands

**Step 5, block 1.** This output was used while working on understand the django project folders. The content below remains in its original sequence for Step 5, block 1.

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

**Step 5, block 2.** This code sample or terminal command was used while working on understand the django project folders. The content below remains in its original sequence for Step 5, block 2.

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

#### How the code works

**Block 1 (text, 11 lines).** In Step 5, understand the django project folders involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 5, output block 1 showed the result rather than another command. Its visible value was `Techmindz_Inventory_Module0_SQLite_Atomic_Update/`, which mattered for understand the django project folders.

**Block 2 (powershell, 1 lines).** In Step 5, understand the django project folders involved `manage.py`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_invent` supply the values or page content for this part of manage.py. These lines are kept together because their order and indentation matter.

#### How we checked it

Not tested yet at Step 5. For Step 5, the proposed way to check understand the django project folders was to revisit `manage.py` and run the corresponding command or feature to see its result. Development continued in Step 6 before a complete result could be claimed.

#### Next

[Step 4: Create the configuration file](#step-4) · [Step 6: Create .env](#step-6)


---

<a id="step-6"></a>

### Step 6: Create .env

#### Before this step

Django now had its packages, but it still needed local settings so it could start safely on this computer. Immediately before this, Step 5 dealt with understand the django project folders; the work here continued the same part of the application.

#### What we did and why

A secret key is a private value Django uses for security-related features. We put local values in .env instead of inside publicly shared code. The setup checks here were meant to show that these settings were available; they did not mean the full inventory system was finished.

#### Code and commands

**Step 6, block 1.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 1.

```powershell
    py --list
    
```

**Step 6, block 2.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 2.

```powershell
    py -V:Astral/CPython3.12.12 -m venv .venv
    
```

**Step 6, block 3.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 3.

```powershell
    .\.venv\Scripts\python.exe --version
    
```

**Step 6, block 4.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 4.

```powershell
    .\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\requirements.txt
    
```

**Step 6, block 5.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 5.

```powershell
Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_inventory_module0_sqlite_atomic\.env
```

**Step 6, block 6.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 6.

```powershell
.\.venv\Scripts\python.exe -m django --version
```

**Step 6, block 7.** This output was used while working on create .env. The content below remains in its original sequence for Step 6, block 7.

```text
6.0.9
```

**Step 6, block 8.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 8.

```powershell
Test-Path .\techmindz_inventory_module0_sqlite_atomic\.env
```

**Step 6, block 9.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 9.

```powershell
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

**Step 6, block 10.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 10.

```env
DJANGO_SECRET_KEY=replace-with-random-secret
```

**Step 6, block 11.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 11.

```powershell
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

**Step 6, block 12.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 12.

```powershell
python .\techmindz_inventory_module0_sqlite_atomic\manage.py check
```

**Step 6, block 13.** This output was used while working on create .env. The content below remains in its original sequence for Step 6, block 13.

```text
System check identified no issues (0 silenced).
```

**Step 6, block 14.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 14.

```powershell
git --version
```

**Step 6, block 15.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 15.

```powershell
.\.venv\Scripts\python.exe -m django --version
```

**Step 6, block 16.** This output was used while working on create .env. The content below remains in its original sequence for Step 6, block 16.

```text
6.0.9
```

**Step 6, block 17.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 17.

```powershell
git --version
```

**Step 6, block 18.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 18.

```powershell
Test-Path .\techmindz_inventory_module0_sqlite_atomic\.gitignore
```

**Step 6, block 19.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 19.

```powershell
Get-Content .\techmindz_inventory_module0_sqlite_atomic\.gitignore
```

**Step 6, block 20.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 20.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status
```

**Step 6, block 21.** This code sample or terminal command was used while working on create .env. The content below remains in its original sequence for Step 6, block 21.

```powershell
git -C .\techmindz_inventory_module0_sqlite_atomic status
```

#### How the code works

**Block 1 (powershell, 2 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `py --list` starts Python or one of its tools from the development terminal.

**Block 2 (powershell, 2 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `py -V:Astral/CPython3.12.12 -m venv .venv` starts Python or one of its tools from the development terminal.

**Block 3 (powershell, 2 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The lines starting with `.\.venv\Scripts\python.exe --version` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 4 (powershell, 2 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–2:** The command `.\.venv\Scripts\python.exe -m pip install -r .\techmindz_inventory_module0_sqlite_atomic\r` starts Python or one of its tools from the development terminal.

**Block 5 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Copy-Item .\techmindz_inventory_module0_sqlite_atomic\.env.example .\techmindz_invent` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 6 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe -m django --version` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 7 (text, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 6, output block 7 showed the result rather than another command. Its visible value was `6.0.9`, which mattered for create .env.

**Block 8 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Test-Path .\techmindz_inventory_module0_sqlite_atomic\.env` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 9 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -c "from django.core.management.utils import get_random_secret_key; print(get_rando` starts Python or one of its tools from the development terminal.

**Block 10 (env, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** `DJANGO_SECRET_KEY=replace-with-random-secret` sets the named value used by the next operation in this file.

**Block 11 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python -c "from django.core.management.utils import get_random_secret_key; print(get_rando` starts Python or one of its tools from the development terminal.

**Block 12 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `python .\techmindz_inventory_module0_sqlite_atomic\manage.py check` starts Python or one of its tools from the development terminal.

**Block 13 (text, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 6, output block 13 showed the result rather than another command. Its visible value was `System check identified no issues (0 silenced).`, which mattered for create .env.

**Block 14 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git --version` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 15 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `.\.venv\Scripts\python.exe -m django --version` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 16 (text, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

In Step 6, output block 16 showed the result rather than another command. Its visible value was `6.0.9`, which mattered for create .env.

**Block 17 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git --version` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 18 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Test-Path .\techmindz_inventory_module0_sqlite_atomic\.gitignore` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 19 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The lines starting with `Get-Content .\techmindz_inventory_module0_sqlite_atomic\.gitignore` supply the values or page content for this part of requirements.txt. These lines are kept together because their order and indentation matter.

**Block 20 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic status` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

**Block 21 (powershell, 1 lines).** In Step 6, create .env involved `requirements.txt`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–1:** The command `git -C .\techmindz_inventory_module0_sqlite_atomic status` works with Git history or a GitHub connection; it does not itself change the website’s Django behaviour.

#### How we checked it

For Step 6 (Create .env), we compared the visible terminal output with the requested action. The visible result for Step 6 was described as “was successful”; this only addressed the create .env check in `requirements.txt`.

#### Next

[Step 5: Understand the Django project folders](#step-5) · [Step 7: Initialize the Git repository](../Module-0/02-git-and-github.md#step-7)


### Check your understanding

Without looking at the code, explain why **environment setup** was needed at this point, and describe what the application did differently after the final step of this chapter.
