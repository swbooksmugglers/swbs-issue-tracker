# Archive Workflow Overview

## Purpose

Archive displays retailer-exclusive books grouped by retailer.

## Workflows

Archive workflows are documented in:
1. `access.md`
2. `retailer-exclusives.md`
3. `filtering.md`

## Role Rules

Archive requires a signed-in user.

Any enabled user with a valid JWT can view Archive.

## Client Entry Points

Archive is available from the header account menu.

Archive route is `/archive`.

## Server Entry Points

Retailer-exclusive books API is served by `/book/retailer-exclusives`.

The API must require authenticated user access.
