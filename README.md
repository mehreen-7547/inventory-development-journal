# AI Inventory & Demand Intelligence System

## A plain-language journal of how I built the project

These are **371 development steps**, rewritten so you can understand and explain them even if you did not remember the original code. The chapters begin with the situation we were in, then each step explains the reason for a change before showing the code.

**Start here:** [How to read and study](HOW_TO_STUDY.md) · [Find a specific step](STEP_INDEX.md) · [Simple word meanings](GLOSSARY.md)

**Scope:** This journal covers Modules 0–5 through Module 5 Chapter 5 (Steps 1–371). Chapter 5 is source-verified at commit `788a158`; 87 purchasing tests passed. Forecasting remains future work.

**History:** The original response-based journal remains on `main`. The earlier rewrite remains on `rewritten-study-journal`. This version is intended for a separate branch named `study-2` (Git branch names cannot contain spaces).

**Reading code:** An earlier command or code sample may be superseded later. Do not rerun historical database resets, password settings, or Git commands just to study.

**Latest checkpoint:** Module 5 Chapter 5 — [Goods Receipt and Close Short](Module-5/06-goods-receipt-and-close-short.md), Steps 361–371, verified source commit `788a158`.

## Module 0 — Foundations

- [Chapter 1: Environment setup](Module-0/01-environment-setup.md) — Steps 1–6
- [Chapter 2: Git and GitHub](Module-0/02-git-and-github.md) — Steps 7–24
- [Chapter 3: Database and migrations](Module-0/03-database-and-migrations.md) — Steps 25–28
- [Chapter 4: Server and admin account](Module-0/04-server-and-admin-account.md) — Steps 29–36
- [Chapter 5: Security checks](Module-0/05-security-checks.md) — Steps 37–41
- [Chapter 6: AI forecasting feasibility test](Module-0/06-ai-forecasting-feasibility-test.md) — Steps 42–45
- [Chapter 7: Module 0 completion](Module-0/07-module-0-completion.md) — Steps 46–55

## Module 1 — Accounts

- [Chapter 1: Accounts app and custom user model](Module-1/01-accounts-app-and-custom-user-model.md) — Steps 56–67
- [Chapter 2: Database setup and admin site](Module-1/02-database-setup-and-admin-site.md) — Steps 68–72
- [Chapter 3: Login](Module-1/03-login.md) — Steps 73–79
- [Chapter 4: Logout](Module-1/04-logout.md) — Steps 80–82
- [Chapter 5: Homepage protection and navigation](Module-1/05-homepage-protection-and-navigation.md) — Steps 83–85
- [Chapter 6: Password change](Module-1/06-password-change.md) — Steps 86–90
- [Chapter 7: Role-based homepage and feedback messages](Module-1/07-role-based-homepage-and-feedback-messages.md) — Steps 91–99
- [Chapter 8: Role security (decorators)](Module-1/08-role-security-decorators.md) — Steps 100–107
- [Chapter 9: Email validation and email setup](Module-1/09-email-validation-and-email-setup.md) — Steps 108–112
- [Chapter 10: Forgot and reset password](Module-1/10-forgot-and-reset-password.md) — Steps 113–127
- [Chapter 11: Employee welcome email and password-setup link](Module-1/11-employee-welcome-email-and-password-setup-link.md) — Steps 128–135
- [Chapter 12: Employee list](Module-1/12-employee-list.md) — Steps 136–138
- [Chapter 13: Create employee](Module-1/13-create-employee.md) — Steps 139–144
- [Chapter 14: Edit employee](Module-1/14-edit-employee.md) — Steps 145–156
- [Chapter 15: Activate, deactivate and resend setup email](Module-1/15-activate-deactivate-and-resend-setup-email.md) — Steps 157–164
- [Chapter 16: Message styling](Module-1/16-message-styling.md) — Steps 165–169
- [Chapter 17: Real email delivery (SMTP)](Module-1/17-real-email-delivery-smtp.md) — Steps 170–178
- [Chapter 18: Shared base template and page styling](Module-1/18-shared-base-template-and-page-styling.md) — Steps 179–194


- [Chapter 19: Publishing the Completed Django Accounts Module to GitHub and Fixing the Home Page Login Redirect Test](Module-1/19-publishing-accounts-and-fixing-homepage-login-redirect-test.md) — Steps 195–203
- [Chapter 20: Testing Django Login, Logout, Inactive Accounts and Authentication Sessions](Module-1/20-testing-login-logout-inactive-accounts-and-sessions.md) — Steps 204–209
- [Chapter 21: Testing Employee Creation, Editing, Deactivation, Administrator Permissions and Setup Emails](Module-1/21-testing-employee-management-and-admin-permissions.md) — Steps 210–216
- [Chapter 22: Testing Unauthorized Employee Changes, Form Validation and Account Reactivation](Module-1/22-testing-unauthorized-employee-changes-form-validation-and-reactivation.md) — Steps 217–223
- [Chapter 23: Testing Password Changes, Password Recovery and Account Setup Emails](Module-1/23-testing-password-changes-recovery-and-account-setup-emails.md) — Steps 224–231

- [Chapter 24: Testing Complete Password Reset and First-Time Employee Password Setup](Module-1/24-testing-complete-password-reset-and-first-time-employee-setup.md) — Steps 232–235

## Module 2 — Supplier and inventory foundations

- [Chapter 1: Supplier setup and list](Module-2/01-supplier-setup-and-list.md) — Steps 236–247 (source published)
- [Chapter 2: Create Supplier](Module-2/02-create-supplier.md) — Steps 248–251 (source published)

- [Chapter 3: Edit Supplier](Module-2/03-edit-supplier.md) — Steps 252–260 (source verified on `main`, commit `2b580fb`)

- [Chapter 4: Activate and Deactivate Supplier](Module-2/04-activate-deactivate-supplier.md) — Steps 261–269 (source verified on `main`, commit `1c3f0ec`)

- [Chapter 5: Product Model, List and Registration](Module-2/05-product-model-list-registration.md) — Steps 270–272 (source commit `796ccb5`)
- [Chapter 6: Edit Product](Module-2/06-edit-product.md) — Step 273 (source commit `796ccb5`)

- [Chapter 7: Activate and Deactivate Product](Module-2/07-activate-deactivate-product.md) — Step 274 (source commit `ff55d4f`)

- [Chapter 8: Stock Movement and Stock Service](Module-2/08-stock-movement-ledger-and-history.md) — Steps 275–279 (source commit `110c30f`; 80 tests passed)

- [Chapter 9: Opening Stock Registration](Module-2/09-opening-stock-registration.md) — Steps 280–283 (source commit `75fce94`; 87 tests passed)

- [Chapter 10: Module 2 Closing Test Correction](Module-2/10-module-2-closing-test-correction.md) — Step 284 (source commit `61c812b`; 87 tests passed)

## Module 3 — Stock adjustments

- [Chapter 1: Stock Adjustments and Product Action Modes](Module-3/01-stock-adjustments-and-product-modes.md) — Journal Steps 285–290 (interactive Steps 418–423; source commit `983d4b0`)

- [Chapter 2: Stock Ledger Integrity](Module-3/02-stock-ledger-integrity.md) — Journal Steps 291–294 (interactive Steps 424–427; source commit `5d662eb`)

## Module 4 — Sales

- [Chapter 1: Sales Setup](Module-4/01-sales-setup.md) — Journal Steps 295–306 (interactive Steps 428–444; source commits `18022db`, `7af5b5e`)

- [Chapter 2: Recording Sales](Module-4/02-recording-sales.md) — Journal Steps 307–316 (interactive Steps 445–457; source commit `b2ae5c6`; 138 tests passed)

- [Chapter 3: Sales Recording Interface](Module-4/03-sales-recording-interface.md) — Journal Steps 317–321 (interactive Steps 458–460; source commit `8edb896`; 170 tests passed)

- [Chapter 4: Sales History](Module-4/04-sales-history.md) — Journal Steps 322–326 (interactive Steps 461–462; source commit `664cf33`; 182 tests passed)

- [Chapter 5: Sale Voiding and Stock Reversal](Module-4/05-sale-voiding-and-stock-reversal.md) — Journal Steps 327–331 (interactive Steps 463–467; source commit `f202c39`; test outputs not supplied)

**Module 4 final verification:** All 196 Django tests passed, 0 system-check issues and no pending migrations after Chapter 5 source commit `f202c39`.

## Module 5 — Purchasing

- [Chapter 1: Purchase Order Setup](Module-5/01-purchase-order-setup.md) — Journal Steps 332–336 (interactive Steps 469–473; source commit `45bd7a2`; user reported purchasing checks passed)

- [Chapter 2: Purchase Order Creation Service](Module-5/02-purchase-order-creation-service.md) — Journal Steps 337–342 (interactive Steps 474–479; source commit `5160b69`; 221 full-project tests passed)

- [Maintenance after Module 5 Chapter 2: Sales UI cleanup](Module-5/03-sales-ui-maintenance-before-purchase-interface.md) — Steps 343–344 (latest source `6cd17f6`; 99 Sales tests passed)

- [Chapter 3: Purchase Order Creation Interface](Module-5/04-purchase-order-creation-interface.md) — Journal Steps 345–352 (interactive Steps 480–487; source commit `f3063b6`; 39 purchasing tests passed)

- [Chapter 4: Purchase Order Management Interface](Module-5/05-purchase-order-management-interface.md) — Journal Steps 353–360 (source commits `06694bb`, `634c41e`; final 56-test rerun not supplied)

- [Chapter 5: Goods Receipt and Close Short](Module-5/06-goods-receipt-and-close-short.md) — Steps 361–371 (source `788a158`; 87 purchasing tests passed)
