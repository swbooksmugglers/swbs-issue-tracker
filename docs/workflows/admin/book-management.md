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
6. Book type
7. MSRP
8. Publisher

Optional book fields are:
1. Signed
2. Era
3. Series
4. Sub-series
5. Notes
6. Exclusive
7. Case type
8. Limitation
9. Limited edition type

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

## Edit Book Workflow

1. User searches for a book
2. User clicks edit icon for a search result
3. Client loads book detail and lookup data
4. Client displays Edit Book form
5. User updates fields
6. User clicks Update
7. Client calls update book API
8. API updates book
9. Client closes modal
10. Client displays success message

## Save As New Workflow

1. User searches for a book
2. User opens Edit Book form
3. User changes ISBN or other fields
4. User clicks Save as New
5. Client calls create book API with current form values
6. API creates a new book record
7. Client closes modal
8. Client displays success message

## Delete Book Workflow

1. Admin searches for a book
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

## Failure Workflow

1. If lookup loading fails, display failed-to-load form data message
2. If search fails, display search error message
3. If create fails, keep form open and display API error message
4. If update fails, keep form open and display API error message
5. If delete is blocked or fails, return to edit view and display API error message
6. If network fails, display network error message
