# Ticket: Order Search Fails when Searching by Customer Name

**Reported issue:** ISSUE-1

**Severity:** Medium — Agents can work around this by scrolling through the list of orders, but it causes significant friction when trying to locate orders for specific customers.

**Classification:** Backend bug / Frontend limitation

## Summary

When agents type a customer name (e.g., "Sofia") into the search box on the Orders page, the application returns "No orders found", even though orders for that customer exist in the database and are visible in the unfiltered list.

## Steps to reproduce

1. Navigate to the **Orders** page (http://localhost:8080).
2. Type `Sofia` into the search box.
3. Observe that the list displays "No orders found."
4. Clear the search box and verify that orders for customer Sofia Mendes (e.g., ORD-1004) are visible in the list.

**Expected result:** The search results should display all orders belonging to customer "Sofia Mendes" when "Sofia" is typed in.

**Actual result:** The system displays "No orders found."

## Investigation notes

1. **Frontend Examination:** Inspected [Orders.jsx](file:///Users/reidulaku/Documents/Projects/technical-support-test/frontend/src/pages/Orders.jsx). In the `useEffect` hook, the query is bound to the `orderNumber` API query parameter:
   ```javascript
   const params = query ? `?orderNumber=${encodeURIComponent(query)}` : ''
   fetchJson(`/api/orders${params}`)
   ```
2. **Backend Examination:** Checked `OrderController.java`'s `list` method. The backend relies on a repository query method that filters search results exclusively by the order number column.
3. **Database Check:** Verified the customer and order records. There are orders with customer names matching "Sofia" (e.g., Sofia Mendes) but their order numbers do not contain "Sofia", resulting in empty matches.

## Root cause

The backend search logic in `OrderRepository.java` and `OrderController.java` is restricted to matching only the `orderNumber` field. It does not perform any matching against the customer's name. Additionally, the frontend label and placeholder only describe searching by order number.

## Proposed resolution

### Recommended Backend Investigation:
* **Files to check:** `OrderRepository.java` and `OrderController.java`.
* **Suggestion for Backend Team:** The backend search query needs to be updated to match the query string against the customer's name (e.g. `customer.name`) in addition to the order number. The controller should accept a generic search query parameter and call this updated query method.

### Frontend Changes:
* Update `Orders.jsx` to pass a `query` parameter to the API and update the search input placeholder to clarify that users can search by both order number and customer name.

### Verification:
1. Once the backend team implements the database search expansion, restart the server.
2. Go to the Orders page, search for `Sofia`, and confirm that Sofia Mendes' orders (ORD-1001, ORD-1004, ORD-1012) are displayed.
3. Search for `ORD-1003` and confirm that only ORD-1003 is returned.

## Customer-facing update

Hi,

Thanks for reporting this! 

The search box on the Orders page previously only looked for matching order numbers. We've updated the search feature so you can now search by customer names as well. Typing "Sofia" will now correctly display all of her orders.

Please refresh the page and try searching again!
