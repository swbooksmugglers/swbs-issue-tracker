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

Settings, Support, About, and Sign out are visible to all signed-in users.

## Navigation Tabs

Header renders a tab row below the top bar with: Home, Archive, My Library.

My Library opens a menu with: My Collection, My Reading List.

Active tab is highlighted based on the current route.

## Client Entry Points

Authenticated pages render the shared Header component.

Header reads the stored token directly to populate the account menu.
