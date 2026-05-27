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

Settings, Submit a Bug, About, and Sign out are visible to all signed-in users.

## Navigation Tabs

Header renders a tab row below the top bar with four tabs: Home, Archive, My Library, and Codex.

**Archive** opens a sub-menu with: Catalog, Exclusives, Legends Reference, SFBC Reference, Timeline.

**My Library** opens a sub-menu with: My Collection, My Events, My Reading List.

**Codex** opens a sub-menu with: FAQ, Gutter Codes, SFBC Guide.

Active tab is highlighted based on the current route. Routes `/support`, `/gutter-codes`, and `/sfbc-guide` all activate the Codex tab.

## Client Entry Points

Authenticated pages render the shared Header component.

Header reads the stored token directly to populate the account menu.
