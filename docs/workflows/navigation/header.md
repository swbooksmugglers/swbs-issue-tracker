# Header Workflow

## Rules

Header reads the stored token directly.

Header displays app name.

Header displays global search.

Header displays a credits earned badge showing the user's accumulated activity points.

Header displays a notification bell icon button.

Header displays the account menu button showing the user's rank and name.

## Client Side Validation

Client side validation occurs when Header renders.

On render:
1. Decode token payload
2. Read first name and role from token
3. Read display name, faction, and collected item count from theme preference context
4. Compute rank from faction and collected item count
5. Display rank and name as the account menu button label (e.g. "Padawan Herm")
6. Use display name when set, fall back to first name from token

## Navigation Tabs

Header renders four navigation tabs below the top bar:

1. **Home** (`/home`) — navigates directly
2. **Archive** — opens Archive sub-menu:
   - Catalog (`/archive/catalog`)
   - Exclusives (`/archive/exclusives`)
   - Legends Reference (`/archive/legends`)
   - SFBC Reference (`/archive/sfbc`)
   - Timeline (`/archive/timeline`)
3. **My Library** — opens My Library sub-menu:
   - My Collection (`/collection`)
   - My Events (`/events`)
   - My Reading List (`/reading-list`)
4. **Codex** — opens Codex sub-menu:
   - FAQ (`/support`)
   - Gutter Codes (`/gutter-codes`)
   - SFBC Guide (`/sfbc-guide`)

The active tab is determined by the current route. The Codex tab is active when the current path is `/support`, `/gutter-codes`, or `/sfbc-guide`.

## Account Menu Workflow

Account menu button shows the user's rank and name. Clicking it opens the account menu. See `account-menu.md` for the full workflow.

## Search Workflow

Header search is documented in `docs/context/workflows/search/header-search.md`.

## Failure Workflow

If token cannot be decoded, authenticated route behavior should clear stored token and redirect to Sign In page through API/auth handling.
