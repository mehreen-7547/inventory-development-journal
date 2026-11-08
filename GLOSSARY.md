# Common words used in the journal



Every term below is written for someone revising Django from the beginning.



**Django:** The Python framework used to build our website. It handles common tasks such as pages, users and database access.

**Python:** The programming language in which Django and our application logic are written.

**App:** A group of related Django files. We put employee-login features in an app called `accounts`.

**Project:** The whole Django website and its main settings. Ours contains several apps.

**Browser:** The program someone uses to open the website, such as Chrome.

**URL:** The address of a page, such as `/accounts/login/`. Django checks URL rules to find the right code.

**View:** A Python function or class that receives the browser's request and decides what to return.

**Template:** The HTML file that decides how a page looks and what words or fields are shown.

**Model:** A Python description of information to store, such as an employee's username, email and role.

**Database:** Where the app stores records so they remain after the website restarts.

**Migration:** A saved instruction to create or change database tables. `makemigrations` writes the instruction; `migrate` follows it.

**AbstractUser:** Django's ready-made starting point for a user record. We add an employee role to it.

**CustomUser:** The user record used by our project, based on `AbstractUser`.

**AUTH_USER_MODEL:** A setting that tells Django to use our `CustomUser` for login and accounts.

**Form:** The fields someone fills in, such as username and password. Django checks their values on submission.

**GET request:** A browser asking to view a page, usually before someone submits a form.

**POST request:** A browser sending form information or asking the server to perform an action.

**Authentication:** Checking who is signing in, usually using a username and password.

**Permission / role:** Checking what the signed-in person is allowed to see or change.

**Decorator:** A piece of Python code placed in front of a view, such as a guard that checks permission before opening a page.

**Session:** How Django remembers that a visitor is already signed in while moving between pages.

**CSRF token:** A hidden safety value Django checks when an HTML form changes something.

**is_active:** A yes/no field that can stop a user from signing in without deleting their account record.

**Django Admin:** Django's ready-made back-office site. Our own employee pages are separate from it.

**Console email:** An email printed in the VS Code terminal for testing, not delivered to a real inbox.

**SMTP:** A method programs use to ask an email provider, such as Gmail, to send a message.

**.env:** A private text file with settings for this computer, such as which Gmail account sends mail. It should not be uploaded.

**python-dotenv:** A package that lets Python read settings from a `.env` file.

**Git:** A tool that keeps a history of changes to files.

**Commit:** A saved point in Git history on your computer.

**Push:** Send local Git commits to a connected repository on GitHub.

**Branch:** A separate line of changes in one Git repository, so we can work without changing `main`.

**Virtual environment:** A private Python-and-packages folder for one project, usually named `.venv`.
