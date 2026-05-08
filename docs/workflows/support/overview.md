# Support Workflow Overview

## Purpose

Support workflows let signed-in users access the User Guide, FAQ, submit bug reports, and view app version information.

## Workflows

Support workflows are documented in:
1. `about-version.md`
2. `bug-report.md`

## Role Rules

Support (page), Submit a Bug, and About are available to signed-in users from the header account menu.

The Support page (`/support`) requires authentication. It contains User Guide and FAQ sections.

Bug report submission requires authenticated user access.

Version lookup does not require authentication at the API level, but is currently opened from the authenticated header menu.

## Server Entry Points

Support APIs are served under `/support`.

Version API is `/support/version`.

Bug report API is `/support/bug`.
