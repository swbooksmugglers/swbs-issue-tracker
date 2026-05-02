# Search Results Workflow

## Rules

Search Results requires a signed-in user with a valid JWT.

Search query and optional filters come from route state.

## Client Side Validation

On page load:
1. If no token exists, redirect to Sign In page
2. If query is fewer than 3 characters after trimming whitespace, do not call search API
3. If query is valid, clear previous results and call the search API with the trimmed query and any active filters

## Server Side Validation

The search API must validate:
1. Caller is authenticated
2. Query is at least 3 characters

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

1. Search Results page receives token, query, and optional filters from route state
2. Client validates query length is at least 3 characters after trimming whitespace
3. Client calls search API with `scope=all` and any active filter params
4. API returns matching metadata results and, when enabled, summary semantic results
5. Client renders results table
6. Client displays book cover when available
7. User can open a read-only book details dialog from the row action icon
8. User can click **Filter** to open the filter dialog and narrow results server-side
9. User can add a row's book to their collection from the row action icon

Displayed result fields:
1. Cover
2. Title
3. ISBN
4. Author
5. Publisher
6. Publication date
7. Row action to add to collection
8. Row action to view details

## Add To Collection Workflow

Add to collection is a per-row action.

On add:
1. Client calls `POST /api/collection/items` with the selected `book_id`
2. API creates a new collected item scoped to the authenticated user
3. Duplicate copies of the same book are allowed
4. Client displays a success snackbar
5. Snackbar includes an edit action for reading status, notes, signed, purchase price, date purchased, and customizations

Adding a book to a collection must not edit the book entity.

## Filter Workflow

Filter behaviour is documented in `docs/context/workflows/search/advanced-search.md`.

## Empty State Workflow

1. If API returns no results, display empty-results message

## Failure Workflow

1. If API call is aborted, do not display an error
2. If search fails, display search error message
3. If token is invalid, clear stored token and redirect to Sign In page
