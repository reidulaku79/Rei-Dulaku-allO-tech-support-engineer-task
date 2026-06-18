# Ticket: Order ORD-1007 Fails to Load with System Error Banner

**Reported issue:** ISSUE-3

**Severity:** High — The agent cannot view the details of order ORD-1007, blocking them from answering customer questions about product delivery.

**Classification:** Backend bug

## Summary

When attempting to open the details page for order ORD-1007, the UI displays the error banner: "Could not load this order. Please try again later." Other orders load successfully.

## Steps to reproduce

1. Navigate to the **Orders** page.
2. Click on order **ORD-1007**.
3. Observe the system displays the error banner: "Could not load this order. Please try again later."

**Expected result:** The order detail page for ORD-1007 should load successfully.

**Actual result:** The page displays a generic error banner.

## Investigation notes

1. **API Logs:** Checked the backend application logs and network tab. Fetching `/api/orders/7` (which corresponds to ORD-1007) returns an HTTP 500 Internal Server Error.
2. **Backend Code Analysis:** Checked `OrderService.java`. The `toDetail` method handles formatting of the delivery date when an order status is marked as `DELIVERED`.
3. **Database Seed Data:** Checked the record for order `ORD-1007` in `data.sql`. The order status is marked as `'DELIVERED'`, but the actual delivery date column is set to `NULL`.
4. **Conclusion:** Since the status is `'DELIVERED'`, the backend logic attempts to format the delivery date. Because the date is `NULL`, this results in a `NullPointerException` (HTTP 500 error).

## Root cause

In `OrderService.java`'s `toDetail` method, the code tries to format the delivery date field whenever the order's status is `DELIVERED` without verifying if a delivery date is registered. In the case of ORD-1007, the delivery date is `NULL`, which causes a crash.

## Proposed resolution

### Recommended Backend Investigation:
* **File to check:** `OrderService.java`.
* **Suggestion for Backend Team:** The `toDetail` method needs to check that `order.getDeliveredOn()` is not null before attempting to format the delivery date. This will prevent the `NullPointerException` when an order status is marked as `DELIVERED` but lacks a delivery timestamp.

### Verification:
1. Once the backend team implements the null check, restart the server.
2. Go to the orders page and click on **ORD-1007**.
3. Verify that the order detail page opens successfully and does not display any errors. The "Delivered on" info row should be absent or blank.

## Customer-facing update

Hi,

Thanks for reporting this!

Order ORD-1007 was marked as "Delivered" in the system, but it didn't have a specific delivery date filled in. This empty date caused a system error when you tried to open the order details.

We've fixed the system to handle orders with missing dates correctly. You can now open ORD-1007 to check the details and items.
