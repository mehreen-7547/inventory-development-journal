# MODULE 1 — Employee accounts

## Chapter 10: Forgot and reset password (Steps 113–127)

### Before we begin

We already had a way to change a password while signed in. But someone who forgot their password could not use it.

### What we worked on

We built the full forgotten-password journey: request a link, display a confirmation, send a message, open the link, choose a new password and return to login.

### Picture it this way

There are several small pages because each has a different job. Django's built-in reset views handle token checks; our templates decide what the person sees.

### Find a step

- [Step 113: Forgot Password](#step-113)

---

<a id="step-113"></a>

### Step 113: Forgot Password

#### Before this step

A signed-in person can change a known password, but someone who has forgotten it cannot use that screen. The preceding step, Step 112, covered email configuration, so this was the next part of the development sequence.

#### What we did and why

We started a separate “Forgot password?” page where a user can enter an email address. Django then handles the reset process without requiring the old password.

#### Code and commands

**Step 113, block 1.** This code sample or terminal command was used while working on forgot password. The content below remains in its original sequence for Step 113, block 1.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Forgot Password</title>
</head>
<body>

    <h2>Forgot Password</h2>

    <p>Enter your registered email address to reset your password.</p>

    <form method="POST">
        {% csrf_token %}

        {{ form.as_p }}

        <button type="submit">Send Reset Link</button>
    </form>

    <a href="{% url 'login' %}">Back to Login</a>

</body>
</html>
```

#### How the code works

**Block 1 (html, 23 lines).** In Step 113, forgot password involved `templates/accounts/password_reset.html`. We followed the statements in the order shown, so the setup appears before the operation that uses it.

**Lines 1–7:** The HTML element `<!DOCTYPE html>` creates or closes part of the page structure seen by the browser. The HTML element `<html>` creates or closes part of the page structure seen by the browser. The HTML element `<head>` creates or closes part of the page structure seen by the browser. The HTML element `<title>Forgot Password</title>` creates or closes part of the page structure seen by the browser.

**Lines 8–14:** The HTML element `<h2>Forgot Password</h2>` creates or closes part of the page structure seen by the browser. The HTML element `<p>Enter your registered email address to reset your password.</p>` creates or closes part of the page structure seen by the browser. The form sends submitted fields to the server as a POST request, rather than just opening a page. The `{% csrf_token %}` tag places Django’s request-verification value inside this specific form before it is submitted.

**Lines 15–21:** `{{ form.as_p }}` displays the fields of this form as simple paragraph elements. The HTML element `<button type="submit">Send Reset Link</button>` creates or closes part of the page structure seen by the browser. The HTML element `</form>` creates or closes part of the page structure seen by the browser. `<a href="{% url 'login' %}">Back to Login</a>` builds the address from a Django route name, so the link follows the route if its address later changes.

**Lines 22–23:** The HTML element `</body>` creates or closes part of the page structure seen by the browser. The HTML element `</html>` creates or closes part of the page structure seen by the browser.

#### How we checked it

Not tested yet at Step 113. For Step 113, the proposed way to check forgot password was to open the password-related page and try the expected form flow with a test account. Development continued in Step 114 before a complete result could be claimed.

#### Next

[Step 112: Email Configuration](../Module-1/09-email-validation-and-email-setup.md#step-112) · [Step 114: Password Reset URL](#step-114)
