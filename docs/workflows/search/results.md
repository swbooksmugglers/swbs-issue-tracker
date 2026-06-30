# Search Results Workflow

## Rules

Search Results are public for read-only catalog search.

Account-only row actions require a signed-in user with a valid JWT.

Search query and optional filters come from route state.

## Client Side Validation

On page load:
1. If query is fewer than 3 characters after trimming whitespace, do not call search API
2. If query is valid, clear previous results and call the search API with the trimmed query and any active filters
3. Fetch account-only state such as wishlist membership only when a token exists

## Server Side Validation

The search API must validate:
1. Query is at least 3 characters
2. Account-only mutations are authenticated

Search matches books by:
1. ISBN
2. Title
3. Author
4. Publisher
5. Exclusive
6. Era
7. Series
8. Sub-series
9. Book summary semantic similarity when `SEMANTIC_SEARCH_ENABLED=true` and the search scope is `all` or `summaries`

When `SEMANTIC_SEARCH_ENABLED=false`, `scope=all` returns metadata matches only and `scope=summaries` returns an unprocessable entity response.

## Success Workflow

1. Search Results page reads stored token when present; receives query and optional filters from route state
2. Client validates query length is at least 3 characters after trimming whitespace
3. Client calls search API with `scope=all` and any active filter params
4. API returns matching metadata results and, when enabled, summary semantic results
5. Client renders results table
6. Client displays book cover when available
7. User can open a read-only book details dialog from the row action icon
8. User can click **Filter** to open the filter dialog and narrow results server-side (Filter shown when results exist or when filters are active)
9. User can sort results by title, ISBN, author, publisher, or publication date using column headers
10. Signed-in users can add a row's book to their collection from the row action icon
11. Signed-in users can add a row's book to their wishlist, or navigate to the wishlist pre-filtered on that book if it is already on the wishlist
12. Public visitors who choose an account-only action are prompted to sign in or create an account
13. After a valid search, signed-in users see the ISBN Import prompt below results

Displayed result fields:
1. Cover
2. Title
3. ISBN
4. Author
5. Publisher
6. Publication date
7. Row action to add to collection
8. Row action to add to wishlist / view in wishlist
9. Row action to view details

## Add To Collection Workflow

Add to collection is a per-row action.

On add:
1. Client calls `GET /api/collection/items/book/{book_id}/count` for the selected `book_id`
2. If the authenticated user already has one or more copies, the client asks the user to confirm adding another copy
3. Client calls `POST /api/collection/items` with the selected `book_id`
4. API creates a new collected item scoped to the authenticated user
5. Duplicate copies of the same book are allowed
6. Client displays a success snackbar
7. Snackbar includes an edit action for reading status, notes, signed status, purchase price, date purchased, and customizations

Adding a book to a collection must not edit the book entity.

## Add To Wishlist Workflow

Add to wishlist is a per-row action. The row action label and icon reflect current wishlist state, fetched on page load via `GET /api/wishlist/book-ids`.

- If the book is **not** on the wishlist: row shows **Add to Wishlist** (BookmarkAdd icon).
  1. Client calls `POST /api/wishlist/books/{book_id}`
  2. On 201: toast confirms addition; row action swaps to **View in Wishlist**
  3. On 409 (already in collection): toast explains book is already in collection
  4. On 200 with `already_exists`: no-op toast
- If the book **is** on the wishlist: row shows **View in Wishlist** (BookmarkAdded icon).
  1. Client navigates to `/wishlist` with route state `{ preFilter: book.title }` to pre-filter the wishlist page on that book

Adding a book to the collection automatically removes it from the wishlist server-side. The wishlist row action reverts to **Add to Wishlist** on next page load.

## Filter Workflow

Filter behaviour is documented in `docs/context/workflows/search/advanced-search.md`.

## ISBN Import Workflow

Available to any authenticated user after a valid search is performed, regardless of result count.

1. Client displays "Can't find what you're looking for? Import it by ISBN." with an **ISBN Import** button
2. User clicks **ISBN Import** — opens the Import by ISBN modal
3. User enters an ISBN and clicks **Look up**
4. Client calls `GET /api/book/isbn/{isbn}`
5. API normalises ISBN, checks catalog for duplicate (409 if exists), calls ISFDB, fetches Google Books summary
6. If no record found, display error — user can try a different ISBN
7. If found, modal advances to read-only confirmation showing: title, author(s), publisher, book type, ISBN, publication date, MSRP
8. User checks or unchecks **Add to my collection** (checked by default)
9. User clicks **Add** — client calls `POST /api/book`
10. API resolves author and publisher names (lookup-or-create), inserts book, then best-effort adds to collection if requested
11. If collection add fails, book is still added to catalog and user is informed via snackbar to find it in search
12. Modal closes and success message is shown in snackbar

## Empty State Workflow

1. If API returns no results, display empty-results message

## Failure Workflow

1. If API call is aborted, do not display an error
2. If search fails, display search error message
3. If an account-only action is requested without a token, prompt the visitor to sign in or create an account
4. If token is invalid, clear stored token and redirect to Sign In page
