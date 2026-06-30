# Admin Page Workflow Overview

## Purpose

The Admin page provides privileged workflows for managing users and archive data.

The page is available to users with role `admin` or `power_user`.

## Sections

Admin users can access:
1. User Management
2. Audit Log
3. Announcements
4. Archive Import
5. Archive Management
6. Cover Art Management
7. Event Management

Power users can access:
1. Archive Import
2. Archive Management
3. Cover Art Management (UI only — every Cover Art Management server endpoint requires `admin`; see `cover-art-management.md`)

Event Management (Fan Expo Sync, GalaxyCon Sync) and Announcements are admin-only; they are not available to power users.

Users without `admin` or `power_user` role must not access the Admin page.

## Workflows

Admin page workflows are documented in:
1. `access.md`
2. `user-management.md`
3. `audit-log.md`
4. `isbn-import.md`
5. `summary-sync.md`
6. `asin-sync.md`
7. `event-sync.md`
8. `book-management.md`
9. `lookup-management.md`
10. `cover-art-management.md`
11. `announcements.md`

## Role Rules

Power users can create and update archive records and archive categorization lookups.

Admins can create, update, and delete archive records and archive categorization lookups.

Only admins can manage users.

Only admins can view audit logs.

## Client Entry Points

Admin page is available from the header account menu when the signed-in user has role `admin` or `power_user`.

If the user has no token, redirect to the Sign In page.

If the user has role `user`, redirect to the Home page.

## Server Entry Points

Most Admin APIs are served under `/admin`. Event Management is the exception: Fan Expo Sync and GalaxyCon Sync are served under `/api/fanexpo` and `/api/galaxycon` respectively (see `event-sync.md`).

APIs that require admin access must use admin authorization.

APIs that allow admin or power user access must use power-user authorization.

## Failure Handling

If an Admin API returns unauthorized or forbidden, clear the stored token and redirect to the Sign In page.

If an Admin API fails due to a network or server error, keep the user in the current workflow and display an error message.
