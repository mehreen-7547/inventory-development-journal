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
