# Ticket: Customer Page is Blank when Address is Not Set

**Reported issue:** ISSUE-2

**Severity:** High — Agents are completely blocked from viewing details or retrieving callback information for any customer who does not have an address registered.

**Classification:** Frontend bug

## Summary

When opening the customer detail page for a customer with no registered address (e.g., Liam O'Connor), the entire page goes blank and fails to display any customer information or orders.

## Steps to reproduce

1. Navigate to the **Customers** page.
2. Click on the customer name **Liam O'Connor**.
3. Observe that the page renders as a blank white screen.

**Expected result:** The page should load successfully, displaying Liam O'Connor's contact info (email/phone) and order history, with a placeholder or empty section for his shipping address.

**Actual result:** The screen goes completely blank due to an unhandled React error.

## Investigation notes

1. **Console Logs / Error Analysis:** If we look at the browser developer console when loading Liam O'Connor's page, we see a Javascript TypeError: `Cannot read properties of null (reading 'split')`.
2. **Frontend Inspection:** Inspected `CustomerDetail.jsx`. On line 24, the code splits the customer's address:
   ```javascript
   const addressLines = customer.address.split(',').map((line) => line.trim())
   ```
3. **Database Check:** Checked `data.sql` for Liam O'Connor's customer record:
   ```sql
   (2, 'Liam O''Connor', 'liam.oconnor@example.com', '+353 86 123 4502', NULL, '2024-01-08')
   ```
   The address column is set to `NULL`. When the backend returns this customer, `customer.address` is `null`. The frontend attempts to call `.split(',')` on `null`, which crashes the React component.

## Root cause

The frontend component `CustomerDetail.jsx` assumes the customer's address is always a non-null string and calls `.split(',')` on it directly without null checking. Since Liam O'Connor has a `NULL` address in the database, the code crashes during render.

## Proposed resolution

### Code/Database Changes:
Update the frontend file `CustomerDetail.jsx` to perform a null/empty check on `customer.address` before calling split:
```javascript
// Old code:
// const addressLines = customer.address.split(',').map((line) => line.trim())
const addressLines = customer.address ? customer.address.split(',').map((line) => line.trim()) : []
```
In the JSX, conditionalize the rendering of the shipping address block:
```jsx
<div className="detail-card">
  <h3>Shipping address</h3>
  {/* Old code:
  {addressLines.map((line) => (
    <p key={line}>{line}</p>
  ))}
  */}
  {addressLines.length > 0 ? (
    addressLines.map((line) => (
      <p key={line}>{line}</p>
    ))
  ) : (
    <p className="muted">No address registered</p>
  )}
</div>
```

### Verification:
1. Restart the server.
2. Navigate to Customers -> Liam O'Connor.
3. Verify the page displays successfully, showing contact details and order history, with "No address registered" under the shipping address.

## Customer-facing update

Hi,

Thanks for reporting this!

We found that if a customer didn't have an address saved in our system, opening their page would trigger a system error and show a blank screen.

We've fixed this issue. The page will now load correctly even if a customer doesn't have an address. You can now open Liam O'Connor's profile to get his contact info for your callback.
