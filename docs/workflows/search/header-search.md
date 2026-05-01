# Header Search Workflow

## Rules

User must be signed in with a valid JWT.

Search query must be at least 3 characters after trimming whitespace.

Search is submitted from the global header.

The simple header search uses book metadata search.

## Client Side Validation

Client side validation occurs when the Search form is submitted.

On client side validation failure:
1. If query is fewer than 3 characters after trimming, do not navigate
2. Do not trigger a backend call for search

## Server Side Validation

Server side validation must occur at the API level.

The search API must validate:
1. Caller is authenticated
2. Query is present
3. Query is at least 3 characters after trimming

If query is invalid, return an unprocessable entity error.

## Success Workflow

1. User enters search query in header
2. User clicks Search or presses Enter
3. Client trims query
4. Client validates query length is at least 3 characters
5. Client navigates to `/search` with token and query in route state
6. Search Results page performs the backend search with `scope=books`

## Failure Workflow

1. If query is too short, remain on the current page
2. If no valid token exists, authenticated page redirects to Sign In page
