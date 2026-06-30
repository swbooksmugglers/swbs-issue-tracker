# Archive Workflow Overview

## Purpose

Archive displays the public catalog and archive reference pages.

## Workflows

Archive workflows are documented in:
1. `access.md`
2. `exclusives.md`
3. `filtering.md`

## Role Rules

Archive read-only pages are public.

Account-only actions require an enabled user with a valid JWT.

## Client Entry Points

Archive is available from the header navigation menu.

Archive routes include `/archive/catalog`, `/archive/exclusives`, `/archive/legends`, `/archive/sfbc`, and `/archive/timeline`.

## Server Entry Points

Exclusive books API is served by `/book/exclusives`.

Book detail API is served by `/book/{book_id}`.

Read-only archive APIs allow public access. Archive mutation and management APIs require authenticated user access.
