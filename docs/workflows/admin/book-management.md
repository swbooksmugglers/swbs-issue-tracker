# Book Management Workflow

## Rules

Book management access follows `docs/context/auth/roles.md` and `docs/context/auth/permissions.md`.

ISBN must be unique.

Changing ISBN on an existing book must use Save as New instead of Update.

## Client Side Validation

Client side validation occurs in the Manage Books modal.

On search validation failure
1. If query is fewer than 3 characters after trimming whitespace, keep Search button disabled
2. This should not trigger a backend call for book search

On add or edit validation failure
1. If required fields are missing, prevent submission
2. If printing is less than 1, prevent submission
3. If MSRP is less than 0, prevent submission
4. If editing and ISBN has changed, disable Update and emphasize Save as New

Required book fields are:
1. ISBN
2. Title
3. At least one author row with an author and role
4. Publication date
5. Printing
6. Format
7. MSRP
8. Publisher

Optional book fields are:
1. Signed
2. Continuity
3. Era
4. Series
5. Sub-series
6. Notes
7. Exclusive
8. Case type
9. Limitation
10. Limited edition type
11. Summary

Sub-series is optional. If selected, series must also be selected, and the sub-series must belong to that series.

## Server Side Validation

Server side validation must occur at the API level.

Book APIs must validate:
1. Caller has role `admin` or `power_user` for create, read detail, and update
2. Caller has role `admin` for delete
3. Required fields are present
4. Referenced lookup IDs exist through database constraints
5. ISBN is unique for new records
6. Sub-series, when provided, belongs to the selected series

If ISBN already exists when creating a book, return a conflict error.

If updating or deleting a missing book, return not found.

Book summary APIs must validate:
1. Caller has role `admin` or `power_user` for add and edit
2. Caller has role `admin` for delete
3. Summary is present when adding or editing
4. Book exists

Delete conflict handling follows the block-and-notify rule in `docs/context/development/database-patterns.md`.

## Search Book Workflow

1. User opens Manage Books
2. User enters search query
3. User clicks Search
4. Client trims query and validates query length is at least 3 characters
5. Client calls book search API
6. API searches title, ISBN, linked author/contributor names, publisher, exclusive, era, series, and sub-series
7. Client displays matching books

## Add Book Workflow

1. User opens Manage Books
2. User clicks Add New
3. Client loads lookup data if needed
4. Client displays Add Book form
5. User enters required fields
6. User optionally enters additional details
7. User clicks Save
8. Client calls create book API
9. API creates book
10. Client closes modal
11. Client displays success message

## List Books API

`GET /api/admin/books` returns all books ordered by title. Each item contains `id`, `title`, and `author` (the primary author's name, resolved by role priority: author → illustrator → other; `null` if none).

Requires power user access.

Used by the Entity Manager to list books without requiring a search query.

## Edit Book Workflow

1. User selects Books in the Entity Manager
2. Client calls `GET /api/admin/books` and lists all books
3. User clicks edit icon for a book
4. Client loads book detail and lookup data
5. Client displays Edit Book form
6. User updates fields
7. User clicks Update
8. Client calls update book API
9. API updates book
10. Client closes modal
11. Client displays success message

## Save As New Workflow

1. User selects a book in the Entity Manager
2. User opens Edit Book form
3. User changes ISBN or other fields
4. User clicks Save as New
5. Client calls create book API with current form values
6. API creates a new book record
7. Client closes modal
8. Client displays success message

## Delete Book Workflow

1. Admin selects a book in the Entity Manager
2. Admin opens Edit Book form
3. Admin clicks Delete
4. Client opens delete confirmation view
5. Admin types the book title
6. Delete button remains disabled until typed title exactly matches the book title
7. Admin clicks Delete
8. Client calls delete book API
9. API applies block-and-notify delete handling from `docs/context/development/database-patterns.md`
10. If deletion is allowed, API deletes book
11. Client closes modal
12. Client displays success message

## Book Summary API Workflow

The frontend add and edit summary workflow is not implemented yet.

Backend endpoints:
1. `POST /admin/book/{book_id}/summary` adds a summary and embedding
2. `PUT /admin/book/{book_id}/summary` updates a summary and embedding
3. `DELETE /admin/book/{book_id}/summary` clears the summary and embedding
4. `GET /admin/book-summary/google-books/{isbn}` retrieves a suggested summary from Google Books
5. `POST /admin/book-summary/google-books/batch` imports missing summaries from Google Books

Add and edit summary require power user access.

Delete summary requires admin access.

Google Books single-summary retrieval requires power user access and does not write to the database.

Google Books summary import requires power user access. It starts a persisted background job that scans books with no summary, deduplicates by normalized ISBN, skips ISBNs still inside their retry window, fetches by ISBN, saves summaries when found, and continues if an individual transient lookup fails.

The summary import job stops immediately on provider-wide Google Books errors:
1. `403` responses are recorded as `provider_blocked`.
2. `429` responses are recorded as `rate_limited`; use the provider `Retry-After` value when present.

The Admin UI polls the persisted job and displays processed, updated, unavailable, failed, and skipped counts. If the user navigates away and returns, the UI loads the active or latest job.

Summary import jobs store the initiating user ID and email snapshot. Completed or stopped job indicators show the last run timestamp and the initiating email when available.

Summary add, edit, and delete write audit entries with `entity_type = "book summary"`.

Batch summary import saves each found summary as a new `book summary` audit entry.

## ISBN Import Summary Workflow

When ISBN import finds a book through ISFDB, Google Books, or Amazon fallback, the backend should also perform a best-effort Google Books summary lookup using the normalized ISBN when the selected metadata does not already include a summary.

ISBN import canonicalizes valid ISBN-10 and ISBN-13 values to ISBN-13 for duplicate checks, ISFDB lookup fallback values, Google Books metadata lookup, Google Books summary lookup, Amazon lookup, and the imported book form value.

If Google Books returns a summary, include it as `summary` in the import payload.

If Google Books returns no summary or the summary lookup fails, include `summary: ""` and still return the imported metadata from the selected source.

The frontend displays the imported summary in the book form. Saving the book stores the summary and generates its embedding when the summary is not blank.

## Failure Workflow

1. If lookup loading fails, display failed-to-load form data message
2. If search fails, display search error message
3. If create fails, keep form open and display API error message
4. If update fails, keep form open and display API error message
5. If delete is blocked or fails, return to edit view and display API error message
6. If network fails, display network error message
