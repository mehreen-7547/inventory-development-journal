# Account pages and mobile navigation

## Step 548 — Refine account forms and mobile navigation

### Goal
Separate password rules from inputs and make employee forms and mobile navigation consistent.

### MODIFIED — CLASS Django forms; NEW layout
**Files:** `templates/accounts/change_password.html`, `templates/accounts/password_reset_confirm.html`. Render visible fields individually and move the existing validator help text into a separate responsive panel:
```html
<div class="small text-secondary">{{ form.new_password1.help_text|safe }}</div>
```
The text comes from Django's password form; validators, reset tokens and invalid-link handling are unchanged.

**Files:** `templates/accounts/create_employee.html`, `templates/accounts/edit_employee.html`, `templates/accounts/employee_list.html`, `templates/accounts/login.html`, `templates/accounts/password_reset.html`, `templates/accounts/password_reset_complete.html`, `templates/accounts/password_reset_done.html`. Form cards, field labels, compact table, separate status/setup sections. Existing POST actions and CSRF remain.

**Files:** `templates/base.html`, `core/static/core/ui.css`. Mobile close button and backdrop call closeNavigation:
```javascript
function closeNavigation() {
    document.body.classList.remove('sidebar-open');
    const toggle = document.querySelector('.sidebar-toggle');
    if (toggle) toggle.setAttribute('aria-expanded', 'false');
}
```
Escape also closes the sidebar. CSS controls visibility at the mobile breakpoint.

### MODIFIED — NEW regression assertion
**File: `accounts/tests.py`**:
```python
        employee_usernames = [employee.username for employee in response.context['employees']]
        self.assertIn('salesuser', employee_usernames)
        self.assertNotIn('adminuser', employee_usernames)
        self.assertNotContains(response, 'admin@example.com')
```
The logged-in admin appears in the sidebar, not in the employee queryset. Testing the context preserves the exclusion requirement without treating sidebar identity as an employee row.

Verification: 128/129 accounts/core tests initially passed; after correction 12/12 EmployeeManagementTests passed. Source 2036683, 7cb1d20, 8246579, 7e967ac, 5ce8d9b, 3d06b1e, 1c2ff94, 4a0f79e, 9e58245, a4ae8ab, 6c5b5fb, 24a7af6. Mobile verification pending.
