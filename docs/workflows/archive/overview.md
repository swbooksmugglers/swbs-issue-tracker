# Archive Workflow Overview

## Purpose

Archive displays exclusive books grouped by exclusive.

## Workflows

Archive workflows are documented in:
1. `access.md`
2. `exclusives.md`
3. `filtering.md`

## Role Rules

Archive requires a signed-in user.

Any enabled user with a valid JWT can view Archive.

## Client Entry Points

Archive is available from the header account menu.

Archive route is `/archive`.

## Server Entry Points

Exclusive books API is served by `/book/exclusives`.

Book detail API is served by `/book/{book_id}`.

The APIs must require authenticated user access.
