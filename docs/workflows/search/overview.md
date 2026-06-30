# Search Workflow Overview

## Purpose

Search lets visitors find books in the archive by title, ISBN, author, publisher, or exclusive.

## Workflows

Search workflows are documented in:
1. `header-search.md` — global search bar in the header
2. `results.md` — search results page behaviour
3. `advanced-search.md` — filter dialog on the search results page

## Role Rules

Read-only search is public.

Account-only search actions require an enabled user with a valid JWT.

## Client Entry Points

Search is available from the header on public and authenticated pages.

The Filter dialog is available on the Search Results page only.

Search results are displayed on `/search`.

## Server Entry Points

Book search API is served by `GET /api/book/search`.

Filter options are served by `GET /api/book/search-filters`.

ISBN lookup is served by `GET /api/book/isbn/{isbn}`.

Add book to catalog is served by `POST /api/book`.

Search and filter APIs allow public access. ISBN import and add-book APIs require authenticated user access.

## Failure Handling

If an account-only action is selected without a token, prompt the visitor to sign in or create an account.

If an account-only API returns unauthorized or forbidden, clear the stored token and redirect to the Sign In page.

If search API fails due to network or server error, keep the user on Search Results page and display an error message.
