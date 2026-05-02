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
9. Book summary semantic similarity when the search scope is `all` or `summaries`

## Success Workflow

1. Search Results page receives token, query, and optional filters from route state
2. Client validates query length is at least 3 characters after trimming whitespace
3. Client calls search API with `scope=all` and any active filter params
4. API returns matching metadata and summary semantic results
5. Client renders results table
6. Client displays book cover when available
7. User can open a read-only book details dialog from the row action icon
8. User can click **Filter** to open the filter dialog and narrow results server-side

Displayed result fields:
1. Cover
2. Title
3. ISBN
4. Author
5. Publisher
6. Publication date
7. Row action to view details

## Filter Workflow

Filter behaviour is documented in `docs/context/workflows/search/advanced-search.md`.

## Empty State Workflow

1. If API returns no results, display empty-results message

## Failure Workflow

1. If API call is aborted, do not display an error
2. If search fails, display search error message
3. If token is invalid, clear stored token and redirect to Sign In page
