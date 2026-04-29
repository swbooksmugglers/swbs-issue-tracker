# Support Workflow Overview

## Purpose

Support workflows let signed-in users view app version information and submit bug reports.

## Workflows

Support workflows are documented in:
1. `about-version.md`
2. `bug-report.md`

## Role Rules

Support and About are available to signed-in users from the header account menu.

Bug report submission requires authenticated user access.

Version lookup does not require authentication at the API level, but is currently opened from the authenticated header menu.

## Server Entry Points

Support APIs are served under `/support`.

Version API is `/support/version`.

Bug report API is `/support/bug`.
