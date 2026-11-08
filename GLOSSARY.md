# Essential terms



These are the minimum concepts needed to explain the recorded project.



**App** — A Django package that groups a feature's models, views, templates, and URLs, such as `accounts`.

**Project** — The overarching Django configuration, usually including `settings.py` and project-level `urls.py`.

**View** — Python code that receives a request and returns a response; often it loads and saves models then renders a template.

**URL pattern** — A route connecting an incoming path to a view; a named route can be referenced from templates.

**Template** — HTML with Django tags that displays data and forms in the browser.

**Model** — A Python class that describes stored data; migrations apply its schema to the database.

**Migration** — Versioned instructions for changing the database structure. `makemigrations` writes them; `migrate` applies them.

**AbstractUser** — Django's reusable user-model class. Our `CustomUser` inherits its account machinery and adds a role.

**AUTH_USER_MODEL** — Django setting that tells authentication which concrete user class the project uses.

**Authentication** — Establishing who a person is, for example by validating username and password.

**Authorization** — Deciding which authenticated account can perform an action; a role decorator enforces this on the server.

**Session** — Server-side login state linked to a browser so the user need not submit credentials on every page.

**CSRF token** — A form token Django checks to guard against forged state-changing requests.

**ModelForm** — A Django form linked to a model; it validates submitted values before saving a row.

**GET versus POST** — GET normally requests a page; POST submits form data or performs a change.

**Decorator** — A Python function that wraps another function to add behavior, such as login and role checks.

**is_staff versus role** — `is_staff` controls Django Admin eligibility; the application's `role` field represents business roles.

**is_active** — User-account flag used for reversible deactivation without deleting account history.

**Django messages** — Short notices stored during one request and displayed on the following page.

**Console email backend** — Development mail delivery that prints messages in the terminal instead of sending them.

**SMTP** — Mail-delivery protocol used by a server such as Gmail to send actual email.

**Reset token** — A time-limited identifier Django validates before permitting a password change.

**Git stage, commit, push** — Select files, save a local snapshot, then upload commits to a remote repository.

**Virtual environment** — An isolated Python interpreter/package directory for this project.

**.env** — Local configuration file holding sensitive values outside Git; use example values in tracked documentation.


