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
1. If ISBN is empty, keep Import button disabled
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
5. Client calls ISBN import API
6. API searches ISFDB by ISBN
7. API parses publication data
8. API upserts author and publisher when needed
9. API resolves matching book type when possible
10. API returns prefill data
11. Client closes ISBN Import modal
12. Client opens Add Book modal with imported fields prefilled
13. Client displays creation notice for any newly created author or publisher
14. User completes any missing required book fields
15. User saves Add Book form

Prefilled fields can include:
1. Title
2. ISBN
3. Publication date
4. MSRP
5. Notes
6. Author
7. Publisher
8. Book type

## Failure Workflow

1. If ISBN is empty, keep Import button disabled
2. If ISFDB search fails, display API error message
3. If no ISFDB record is found, display API error message
4. If local database update fails, display API error message
5. If network fails, display network error message
6. Keep the ISBN Import modal open
