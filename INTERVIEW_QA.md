# Interview questions and answers

Three questions per module. These are oral-review practice answers, not a claim that every implementation detail is identical to classroom examples.

## Module 0 — Foundations

**1. Why did you use a virtual environment?**  
It isolates the project's Python packages from other applications. The requirements files record dependencies so another developer can recreate the environment. The project uses Python 3.12 and Django 6.0.9.

**2. Why SQLite and environment variables?**  
SQLite is a local file-based relational database suitable for this academic demonstration. Environment variables keep configuration such as the Django secret key separate from committed code. SQLite is not my choice for a high-concurrency production deployment.

**3. What are migrations and why use Git?**  
Migrations describe database schema changes; migrate applies them. Git records source changes and lets me identify a known working version, such as the m8-complete tag. A migration is not a backup of business data.

## Module 1 — Accounts and roles

**4. What roles exist?**  
Admin manages employee accounts, Inventory Manager manages inventory and purchasing and accesses intelligence, and Sales Staff records sales and views their permitted information.

**5. Is hiding a navigation link sufficient security?**  
No. Templates control visibility, but views must enforce permissions on the server. For example, the employee list is restricted to administrators and filters accounts to manager and sales roles.

**6. How does an employee set a password?**  
An administrator creates the account and the system sends a password setup link. Django password forms validate the new password. The reset workflow also supports recovery; the UI separates the requirements from the inputs without changing validation.

## Module 2 — Catalog and stock foundation

**7. How are products and suppliers related?**  
Each product has one supplier in this project's scope. A supplier can supply multiple products. This avoids the complexity of selecting among multiple suppliers for a product.

**8. Why use active/inactive status instead of deleting records?**  
Historical sales, purchases and stock movements still need their original product or supplier references. Deactivation preserves that history while restricting future use according to the relevant workflow.

**9. What is opening stock?**  
It is the initial quantity recorded when registering a product. It establishes the starting inventory and must be consistent with the stock movement history rather than being treated as a sale.

## Module 3 — Stock adjustments

**10. Why is a stock movement ledger necessary?**  
The current stock quantity tells me what is available; the ledger explains how that quantity changed. Adjustments and other stock-changing operations should leave an auditable movement.

**11. Why use database transactions?**  
A stock change and its corresponding movement must succeed together. Django's atomic transaction mechanism rolls back changes if an exception escapes the transaction, preventing a partially recorded operation.

**12. How is an adjustment different from a sale?**  
An adjustment corrects physical inventory, for example after counting stock. A sale represents goods sold to a customer. Both can change stock, but only a sale represents sales activity.

## Module 4 — Sales

**13. What happens when a sale is recorded?**  
The system validates selected products and quantities, records the sale and its lines, and reduces available stock while recording movements. A transaction prevents partially recorded sales.

**14. Why void a sale rather than delete it?**  
Voiding preserves the original transaction and records its reversal. Stock is restored through the reversal workflow rather than silently erasing the sale.

**15. How do staff and manager sales histories differ?**  
Staff are restricted to their own sales. Managers can search sale numbers or employee usernames; staff search only by sale number. The queryset restriction is enforced before search filtering.

## Module 5 — Purchasing

**16. Does creating a purchase order increase stock?**  
No. A purchase order records expected goods. Actual usable inventory increases only when goods are received and recorded.

**17. How does partial receipt work?**  
The receipt form shows ordered, previously received and remaining quantities. The user enters usable and damaged quantities for this receipt. Damaged goods are distinguished from usable stock; the order tracks progress against its ordered quantities.

**18. What is closing short?**  
It ends an order when the remaining goods will not be delivered. The outstanding quantity is not treated as received stock. This is distinct from receiving the full order.

## Module 6 — Dashboards and demo data

**19. Why are dashboards role-specific?**  
Each role needs different information and actions. The manager needs stock and purchasing alerts, staff need sales-related information, and administrators need account management. Server permissions remain necessary independently of dashboard layout.

**20. How are dashboard summaries generated?**  
Django queries existing database records to calculate summaries and select relevant records, such as low-stock products, recent sales and purchase orders needing attention. These summaries are not AI forecasts.

**21. Why use synthetic historical data?**  
It provides repeatable demonstration records without requiring confidential business data. It is useful for testing workflows, but forecast performance on synthetic data does not establish real-world accuracy.

## Module 7 — Demand intelligence

**22. What AI model is used and did you train it?**  
Chronos-Bolt Tiny is a pretrained time-series forecasting model run locally on CPU. I use historical daily sales to generate predictions rather than training a model from scratch or paying for an inference API. Its initial weights may need downloading.

**23. How is reorder quantity calculated?**  
The system estimates lead-time demand, adds safety stock, subtracts on-hand and scheduled incoming stock, rounds up and floors the result at zero. When available it uses the larger of AI median lead-time demand and the recent 28-day average multiplied by lead time. The recommendation does not automatically place an order.

**24. Are all intelligence features AI?**  
No. Chronos produces forecasts; shortage projections and reorder recommendations combine forecasts with inventory rules. Unusual sales detection uses a rolling-average statistical approach. Historical evaluation measures forecasting accuracy rather than predicting demand itself.

## Module 8 — Manager attention and forecast evaluation

**25. What does Needs attention today do?**  
It prioritizes inventory issues for the manager using existing business information. It supports deciding what to investigate rather than automatically modifying stock or creating orders.

**26. How do you evaluate the AI fairly?**  
For each valid product-week in eight completed weeks, use the preceding 60 days to forecast seven future days, without using the actual evaluation week as input. Compare the summed median forecast with the previous seven-day total and zero baseline. Compute absolute weekly errors and average across valid product-weeks; report skipped weeks separately.

**27. Did the AI outperform the baseline?**  
Not in the latest recorded live comparison ending 8 November 2026. Across 384 valid product-weeks, Chronos MAE was 3.40 units, the previous-week baseline 3.23, and zero 4.27. The simple baseline won. This is an honest result on the demo dataset, not proof that AI is always worse or better.

## Verification note

The original m8-complete milestone passed 528 tests. After UI refinement, 528 of 530 tests passed in the full run and the two remaining wording regressions passed in a targeted run after correction. A single clean post-fix full run and outstanding browser/mobile checks are not yet recorded.

Study the corresponding journal chapter for code-level follow-up questions. Answers here summarize the documented project rather than reproduce every source file.
