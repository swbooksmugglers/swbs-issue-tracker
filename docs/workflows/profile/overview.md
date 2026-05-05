# Settings Page Workflow Overview

## Purpose

The Settings page lets a signed-in user manage personal settings and password.

## Sections

Settings page includes:
1. General
2. Change password
3. Appearance

Home page includes:
1. My Activity

General includes display name.

Appearance includes colour mode, faction, and colour theme.

## Workflows

Settings page workflows are documented in:
1. `access.md`
2. `display-name.md`
3. `change-password.md`
4. `appearance.md`
5. `activity.md`

## Role Rules

Settings page requires a signed-in user.

Any enabled user with a valid JWT can access their own profile.

Users cannot access or update another user's profile through Settings page workflows.

Users can view only their own activity entries.

## Client Entry Points

Settings page is available from the header account menu for signed-in users.

If the user has no token, redirect to the Sign In page.

## Server Entry Points

Profile APIs are served under `/profile`.

Profile APIs must require authenticated user access.

The user ID must come from the validated JWT payload.

The activity API must filter audit logs by the validated JWT user ID.

## Failure Handling

If a Profile API returns unauthorized or forbidden, clear the stored token and redirect to the Sign In page.

If a Profile API fails due to a network or server error, keep the user in the current workflow and display an error message.
