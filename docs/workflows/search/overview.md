# Search Workflow Overview

## Purpose

Search lets signed-in users find books in the archive by title, ISBN, author, publisher, or retailer.

## Workflows

Search workflows are documented in:
1. `header-search.md`
2. `results.md`
3. `advanced-search.md`

## Role Rules

Search requires a signed-in user.

Any enabled user with a valid JWT can search books.

## Client Entry Points

Search is available from the header on authenticated pages.

Search results are displayed on `/search`.

## Server Entry Points

Book search API is served by `/book/search`.

The API must require authenticated user access.

## Failure Handling

If search API returns unauthorized or forbidden, clear the stored token and redirect to the Sign In page.

If search API fails due to network or server error, keep the user on Search Results page and display an error message.
