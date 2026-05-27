# Support Workflow Overview

## Purpose

Support workflows let signed-in users access the FAQ, user guides, submit bug reports, and view app version information.

## Workflows

Support workflows are documented in:
1. `about-version.md`
2. `bug-report.md`

## Role Rules

Support content (FAQ, Gutter Codes, SFBC Guide) is accessible to signed-in users from the **Codex** navigation tab in the header.

Submit a Bug and About are accessible from the account menu.

Bug report submission requires authenticated user access.

Version lookup does not require authentication at the API level, but is currently opened from the authenticated account menu.

## Navigation

The Codex tab in the header nav bar opens a sub-menu with:
- **FAQ** → `/support` (FAQs and user guide content)
- **Gutter Codes** → `/gutter-codes` (static reference guide)
- **SFBC Guide** → `/sfbc-guide` (static reference guide)

## Server Entry Points

Support APIs are served under `/support`.

Version API is `/support/version`.

Bug report API is `/support/bug`.
