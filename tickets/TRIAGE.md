# Triage Document

This document outlines the severity and prioritized order of resolution for the six reported issues.

## Severity & Priority Order

| Priority | Issue | Title | Severity | Impact |
| :--- | :--- | :--- | :--- | :--- |
| **1** | [ISSUE-4](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-4.md) | Discounted orders show crazy wrong totals | **Critical** | Financial calculation error that affects invoice totals, customer trust, and dashboard analytics. |
| **2** | [ISSUE-3](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-3.md) | Order ORD-1007 won't open | **High** | System crash (500 Internal Server Error) preventing agents from resolving inquiries about delivered orders. |
| **3** | [ISSUE-2](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-2.md) | Customer page is blank | **High** | UI crash (blank page) blocking agent access to customer contact details. |
| **4** | [ISSUE-6](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-6.md) | Customer charged double (two chairs) | **Medium** | Incorrect data seeding showing duplicate products; raises charge complaints. |
| **5** | [ISSUE-5](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-5.md) | Can't save notes on orders | **Medium** | Integrity/functional issue preventing agent collaboration between shifts. |
| **6** | [ISSUE-1](file:///Users/reidulaku/Documents/Projects/technical-support-test/tickets/ISSUE-1.md) | Order search is completely broken | **Medium** | Usability bottleneck forcing manual scrolling through orders. |

---

## Detailed Justification

### 1. ISSUE-4: Discounted orders show crazy wrong totals (Critical)
* **Impact/Scope:** High. This bug alters invoice totals shown to customers and breaks financial reporting on the executive dashboard.
* **Urgency:** Immediate. Wrong amounts are shown on real orders, potentially causing financial loss or incorrect card charges.
* **Effort:** Low. Correcting the math in `OrderService` is a simple backend fix.

### 2. ISSUE-3: Order ORD-1007 won't open (High)
* **Impact/Scope:** Medium. Affects specific orders marked as delivered without a recorded delivery date, causing a backend NullPointerException and a system error banner.
* **Urgency:** High. An agent is currently blocked from answering a customer inquiry.
* **Effort:** Low. Needs a null-check in `OrderService.java` before formatting the date.

### 3. ISSUE-2: Customer page is blank (High)
* **Impact/Scope:** Medium. Affects any customer without a saved address (like Liam O'Connor), triggering a React rendering crash (white screen).
* **Urgency:** High. Agents cannot see customer telephone numbers or email addresses, blocking customer callbacks.
* **Effort:** Low. Requires a simple defensive null-check on the frontend in `CustomerDetail.jsx`.

### 4. ISSUE-6: Customer charged double — order shows two chairs (Medium)
* **Impact/Scope:** Low. Isolated data seed duplicate record in `data.sql`.
* **Urgency:** Medium. An upset customer claims they were charged double; needs to be clarified and corrected to reassure them.
* **Effort:** Low. Involves deleting a duplicate seed entry.

### 5. ISSUE-5: Can't save notes on orders (Medium)
* **Impact/Scope:** High. Affects all shift handovers because agents cannot record notes on any order.
* **Urgency:** Medium. No system crash occurs, but it impairs support collaboration.
* **Effort:** Low. Quick frontend payload property rename from `text` to `body`.

### 6. ISSUE-1: Order search is completely broken (Medium)
* **Impact/Scope:** High. Affects all agents trying to search orders by customer name.
* **Urgency:** Medium. Workaround exists (scrolling and manually locating the customer/order in the list).
* **Effort:** Low. Requires adding a JPA/JPQL query to look up both order number and customer name, and updating the frontend parameters.
