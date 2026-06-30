# Navigation Workflow Overview

## Purpose

Navigation workflows describe the public and authenticated header, account menu, route access, and shared media behavior.

## Workflows

Navigation workflows are documented in:
1. `header.md`
2. `account-menu.md`
3. `route-access.md`
4. `book-cover.md`

## Role Rules

Header is shown on public archive and Codex pages, and on authenticated app pages.

Admin menu item is visible only for `admin` and `power_user` roles.

Settings, Submit a Bug, About, and Sign out are visible to all signed-in users.

Sign in and Create account actions are visible to public visitors.

## Navigation Tabs

Header renders a tab row below the top bar.

Public visitors see Archive and Codex.

Signed-in users additionally see Home and My Library.

**Archive** opens a sub-menu with: Catalog, Exclusives, Legends Reference, SFBC Reference, Timeline.

**My Library** opens a sub-menu with: My Collection, My Events, My Reading List, My Wishlist.

**Codex** opens a sub-menu with: FAQ, Guides, User Guide. Stats is shown in Codex only for signed-in users.

Active tab is highlighted based on the current route. Routes `/support`, `/guides`, and `/stats` all activate the Codex tab.

## Client Entry Points

Public archive and Codex pages render the shared Header component without requiring a token.

Authenticated pages render the shared Header component with account controls.

Header reads the stored token directly to populate the account menu.
