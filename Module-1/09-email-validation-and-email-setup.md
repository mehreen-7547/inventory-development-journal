# Module 1, Chapter 9: Email validation and email setup

## Step 108: REQUIRED_FIELDS

`REQUIRED_FIELDS = ['email', 'role']` tells Django **which additional information to ask for when creating a superuser through the terminal**.

For example, when you run:

```bash
python manage.py createsuperuser
```

Django prompts you for:

```text
Username: admin
Email address: admin@example.com
Role: admin
Password:
Password (again):
```

This happens because of `REQUIRED_FIELDS`.

#### Why didn't it work for `manager1` and `sales1`?

Because `REQUIRED_FIELDS` is **not a database validation rule**.

| Situation | Does `REQUIRED_FIELDS` enforce email? |
|---|---|
| Creating a superuser through `createsuperuser` | Yes, prompts for it |
| Creating a user through Django Admin | No |
| Creating a user through Python code | No |
| Saving a user to the database | No |

#### What we actually need

```python
email = models.EmailField(unique=True, blank=False)
```

- `EmailField` — validates email format through Django's forms and model validation.
- `unique=True` — prevents two users from having the same email.
- `blank=False` — requires an email in Django forms.

**In short:** `REQUIRED_FIELDS` controls the `createsuperuser` prompts. The email field definition controls validation and database uniqueness.

We'll keep `REQUIRED_FIELDS` and add the email field after correcting the existing accounts.

*Previous: Step 107 (Test Role Security)  |  Next: Step 109 (Enforce Email Validation)*

---
