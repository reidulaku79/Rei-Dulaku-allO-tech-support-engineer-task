# Ticket: Saving Internal Notes on Orders Fails

**Reported issue:** ISSUE-5

**Severity:** Medium — Agents are prevented from adding notes to orders, which hinders shift handovers and internal team communication, but doesn't cause page load failure or financial inaccuracies.

**Classification:** Frontend / Backend integration bug (Payload mismatch)

## Summary

Whenever a support agent tries to add an internal note to an order, clicking the "Save note" button fails and shows the error banner: "Could not save the note. Please try again later."

## Steps to reproduce

1. Open the details page for any order (e.g., ORD-1001).
2. Scroll down to the **Internal notes** section.
3. Type a message in the text box.
4. Click **Save note**.
5. Observe that the note is not added, and the error banner "Could not save the note" appears.

**Expected result:** The note should be successfully saved to the database and displayed in the notes list immediately.

**Actual result:** The operation fails with an error banner.

## Investigation notes

1. **Browser Network Analysis:** Inspected the request payload and response in the browser network tab.
   * Request: `POST /api/orders/1/notes` with body `{"text":"Test note"}`
   * Response: HTTP 400 Bad Request.
2. **Backend Code Analysis:** Inspected `OrderController.java`'s `addNote` method:
   ```java
   @PostMapping("/{id}/notes")
   @ResponseStatus(HttpStatus.CREATED)
   public NoteDto addNote(@PathVariable Long id, @Valid @RequestBody NoteRequest request) { ... }
   ```
3. **DTO Definition Inspection:** Inspected `NoteRequest.java`:
   ```java
   public record NoteRequest(
           @NotBlank(message = "must not be blank")
           @Size(max = 500, message = "must be at most 500 characters")
           String body) {
   }
   ```
4. **Conclusion:** The backend expects a request body containing a property named `body`. However, the frontend is sending a property named `text`. As a result, Spring deserializes the `body` field as `null`. Since `body` is annotated with `@NotBlank`, the validation fails and returns an HTTP 400 Bad Request.

## Root cause

There is a mismatch between the property name used by the frontend and the property name expected by the backend DTO validation:
* Frontend (`OrderDetail.jsx` line 31) sends: `body: JSON.stringify({ text: noteText })`
* Backend DTO (`NoteRequest.java` line 9) expects: `String body`

## Proposed resolution

### Code/Database Changes:
Update the frontend payload in `OrderDetail.jsx` (line 31) to send the key `body` instead of `text`:
```javascript
// Old code:
// body: JSON.stringify({ text: noteText }),
body: JSON.stringify({ body: noteText }),
```

### Verification:
1. Restart the server.
2. Open an order page, enter a test note, and click **Save note**.
3. Verify that the note is added successfully and displayed without any error banners.

## Customer-facing update

Hi,

Thanks for reporting this!

We found a glitch in the portal that prevented new internal notes from saving correctly. 

We've fixed the system, and you can now write and save notes on all orders without any issues.
