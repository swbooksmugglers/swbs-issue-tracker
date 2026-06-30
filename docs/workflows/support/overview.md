# Support Workflow Overview

## Purpose

Support workflows let visitors access the FAQ and guides. Signed-in users can also submit bug reports, view app version information, and open contributor stats.

## Workflows

Support workflows are documented in:
1. `about-version.md`
2. `bug-report.md`

## Role Rules

Support content (FAQ and Guides) is accessible to public visitors from the **Codex** navigation tab in the header.

Stats is accessible from the **Codex** navigation tab only for signed-in users.

Submit a Bug and About are accessible from the account menu.

Bug report submission requires authenticated user access.

Version lookup does not require authentication at the API level, but is currently opened from the authenticated account menu.

## Navigation

The Codex tab in the header nav bar opens a sub-menu with:
- **FAQ** → `/support` (FAQs and user guide content)
- **Guides** → `/guides` (static reference guides)
- **User Guide** → external user guide link
- **Stats** → `/stats` (signed-in users only)

## Server Entry Points

Support APIs are served under `/support`.

Version API is `/support/version`.

Bug report API is `/support/bug`.
