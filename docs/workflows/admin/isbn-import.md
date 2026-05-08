# ISBN Import Workflow

## Rules

ISBN Import requires role `admin` or `power_user`.

Import searches ISFDB by ISBN.

Imported data pre-fills the Add Book form.

Import can create missing author and publisher records.

Import does not create the book record until the user saves the Add Book form.

## Client Side Validation

Client side validation occurs when the ISBN Import modal is submitted.

On client side validation failure
1. If ISBN is fewer than 3 characters after trimming whitespace, keep Import button disabled
2. This should not trigger a backend call for ISBN import

## Server Side Validation

Server side validation must occur at the API level.

The ISBN import API must validate:
1. Caller has role `admin` or `power_user`
2. ISBN is present
3. ISFDB returns a usable publication record

The API removes ISBN hyphens before searching.

If ISFDB search fails, return bad gateway.

If ISFDB returns no matching publication, return not found.

If author or publisher from ISFDB does not exist locally, create it.

## Success Workflow

1. Admin or power user opens Admin page
2. User clicks ISBN Import
3. User enters ISBN
4. User clicks Import
5. Client trims ISBN and validates length is at least 3 characters
6. Client calls ISBN import API
7. API searches ISFDB by ISBN
8. API parses publication data
9. API upserts author and publisher when needed
10. API resolves matching book type when possible
11. API returns prefill data
12. Client closes ISBN Import modal
13. Client opens Add Book modal with imported fields prefilled
14. Client displays creation notice for any newly created author or publisher
15. User completes any missing required book fields
16. User saves Add Book form

Prefilled fields can include:
1. Title
2. ISBN
3. Publication date
4. MSRP
5. Notes
6. Summary
7. Author
8. Publisher
9. Format

## Summary Import Workflow

Summary Import requires role `admin` or `power_user`.

The batch import scans local books that have no summary, looks up Google Books by each book ISBN, and saves the returned summary when one is found.

Missing Google Books summaries and individual lookup failures do not stop the batch. The API returns checked, updated, unavailable, and failed counts for the client success message.

## Failure Workflow

1. If ISBN is too short, keep Import button disabled
2. If ISFDB search fails, display API error message
3. If no ISFDB record is found, display API error message
4. If local database update fails, display API error message
5. If network fails, display network error message
6. Keep the ISBN Import modal open
