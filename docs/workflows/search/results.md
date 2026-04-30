# Search Results Workflow

## Rules

Search Results requires a signed-in user with a valid JWT.

Search query comes from route state.

Search results can be filtered client side.

## Client Side Validation

Client side validation occurs when Search Results page loads.

On client side validation:
1. If no token exists, redirect to Sign In page
2. If query is fewer than 3 characters after trimming whitespace, do not call search API
3. If query is valid, clear previous results and filter state
4. Call search API with trimmed query

Client-side result filtering matches:
1. Title
2. Author
3. Publisher
4. Exclusive

Client-side result filtering is inactive until filter text is at least 3 characters after trimming whitespace.

## Server Side Validation

Server side validation must occur at the API level.

The search API must validate:
1. Caller is authenticated
2. Query is at least 3 characters

Search should match:
1. ISBN
2. Title
3. Author
4. Publisher
5. Exclusive

## Success Workflow

1. Search Results page receives token and query
2. Client validates query length is at least 3 characters after trimming whitespace
3. Client calls search API
4. API searches books by ISBN, title, author, publisher, or exclusive
5. API returns matching books ordered by title
6. Client renders results table
7. Client displays book cover when available
8. User can enter a filter of at least 3 characters to narrow results locally

Displayed result fields are:
1. Cover
2. Title
3. ISBN
4. Author
5. Publisher
6. Exclusive
7. Publication date
8. MSRP
9. Pre-signed status
10. Notes

## Empty State Workflow

1. If API returns no results, display empty-results message
2. If local filter excludes all results, display no-results-match-filter message

## Failure Workflow

1. If API call is aborted, do not display an error
2. If search fails, display search error message
3. If token is invalid, clear stored token and redirect to Sign In page
