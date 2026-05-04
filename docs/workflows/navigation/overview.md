# Navigation Workflow Overview

## Purpose

Navigation workflows describe the authenticated header, account menu, route access, and shared media behavior.

## Workflows

Navigation workflows are documented in:
1. `header.md`
2. `account-menu.md`
3. `route-access.md`
4. `book-cover.md`

## Role Rules

Header is shown on authenticated app pages.

Admin menu item is visible only for `admin` and `power_user` roles.

Profile, Archive, Collection, Support, About, and Sign out are visible to signed-in users.

## Client Entry Points

Authenticated pages render the shared Header component.

Header reads the stored token directly to populate the account menu.
