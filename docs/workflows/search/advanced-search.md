# Advanced Search Workflow

## Rules

Advanced search lets signed-in users choose whether a search should target book metadata, summaries, or both.

User must be signed in to open the header and access Advanced search.

Search query must be at least 3 characters after trimming whitespace.

## Client Side Validation

Client side validation occurs in the Advanced Search dialog.

On click:
1. Open Advanced Search dialog
2. Display a query field and scope selector

On submit validation failure:
1. If query is fewer than 3 characters after trimming, do not navigate
2. Do not trigger a backend call

## Server Side Validation

Server side validation occurs in the book search API.

The search API must validate:
1. Caller is authenticated
2. Query is present
3. Query is at least 3 characters after trimming
4. Scope is one of `books`, `summaries`, or `all`

## Current Workflow

1. User clicks Advanced search in the header
2. Client opens Advanced Search dialog
3. User enters a query
4. User selects Books, Summaries, or All
5. User clicks Search
6. Client navigates to `/search` with token, query, and scope in route state
7. Search Results calls `/api/book/search?q=<query>&scope=<scope>`

## Future Workflow Notes

Supported scopes:
1. `books` searches title, ISBN, author/contributor names, publisher, exclusive, era, series, and sub-series
2. `summaries` searches summary embeddings with vector similarity
3. `all` combines book metadata and summary matches
