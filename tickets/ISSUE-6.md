# Ticket: Duplicate Item Registered on Order ORD-1003

**Reported issue:** ISSUE-6

**Severity:** Medium/High — Causes billing complaints and double charge disputes, but has no code defect (purely a data entry issue).

**Classification:** Data issue

## Summary

Customer Jonas Weber reported that order ORD-1003 contains two lines of "Ergonomic Office Chair" with a total price of €578.00, whereas he ordered exactly one chair for €289.00.

## Steps to reproduce

1. Navigate to the **Orders** page.
2. Select order **ORD-1003**.
3. Observe the items list: "Ergonomic Office Chair" is listed twice as two separate rows, and the total is calculated as €578.00.

**Expected result:** The order should contain only one line of "Ergonomic Office Chair" for €289.00, and the total should be €289.00.

**Actual result:** The order contains two identical item rows, doubling the total to €578.00.

## Investigation notes

1. **Database Check:** Inspected the database seed file `src/main/resources/data.sql`.
2. **Analysis of Order Items Inserts:** Noticed that there are two separate lines inserting the product "Ergonomic Office Chair" with item IDs 5 and 18, both referencing order ID 3 (which corresponds to order number ORD-1003).
3. **Check for other items:** Looked at all other lines in `data.sql` to verify if they were correct. Item ID 18 is indeed a duplicate insert.

## Root cause

A duplicate record for the "Ergonomic Office Chair" product was accidentally entered in the order items seed dataset in `data.sql` referencing the same order ID (order ID 3). This duplicate record doubles the calculated subtotal and total on the order.

## Proposed resolution

### Recommended Database Changes:
* **File to check:** `src/main/resources/data.sql`.
* **Suggestion for Backend / DB Team:** Remove the duplicate order item line (item ID 18) that links the "Ergonomic Office Chair" product to order ID 3, and ensure the statement is closed correctly with a semicolon.

### Verification:
1. Rebuild and restart the application with `./gradlew bootRun` (this resets the in-memory database and loads the updated `data.sql`).
2. Go to the orders page and click on **ORD-1003**.
3. Verify that there is only one "Ergonomic Office Chair" line, and the total is corrected to €289.00.

## Customer-facing update

Hi Jonas,

Thanks for reaching out!

We looked into order ORD-1003 and found that a duplicate item was accidentally added to your order records, which doubled the displayed price. 

We have removed the extra chair from your order invoice. The total is now correctly shown as €289.00. You were only charged for one chair.

Let us know if you need anything else!
