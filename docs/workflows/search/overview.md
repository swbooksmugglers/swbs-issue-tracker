# Search Workflow Overview

## Purpose

Search lets signed-in users find books in the archive by title, ISBN, author, publisher, or exclusive.

## Workflows

Search workflows are documented in:
1. `header-search.md` — global search bar in the header
2. `results.md` — search results page behaviour
3. `advanced-search.md` — filter dialog on the search results page

## Role Rules

Search requires a signed-in user.

Any enabled user with a valid JWT can search books.

## Client Entry Points

Search is available from the header on authenticated pages.

The Filter dialog is available on the Search Results page only.

Search results are displayed on `/search`.

## Server Entry Points

Book search API is served by `GET /api/book/search`.

Filter options are served by `GET /api/book/search-filters`.

ISBN lookup is served by `GET /api/book/isbn/{isbn}`.

Add book to catalog is served by `POST /api/book`.

All APIs require authenticated user access.

## Failure Handling

If search API returns unauthorized or forbidden, clear the stored token and redirect to the Sign In page.

If search API fails due to network or server error, keep the user on Search Results page and display an error message.
