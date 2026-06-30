# Header Workflow

## Rules

Header reads the stored token directly.

Header displays app name.

Header displays global search.

Public visitors see Sign in and Create account actions.

Signed-in users see a credits earned badge showing the user's accumulated activity points.

Signed-in users see a notification bell icon button.

Signed-in users see the account menu button showing the user's rank and name.

## Client Side Validation

Client side validation occurs when Header renders with a stored token.

When a token exists:
1. Decode token payload
2. Read first name and role from token
3. Read display name, faction, and collected item count from theme preference context
4. Compute rank from faction and collected item count
5. Display rank and name as the account menu button label (e.g. "Padawan Herm")
6. Use display name when set, fall back to first name from token

When no token exists:
1. Render public navigation
2. Hide account-only navigation and controls
3. Show Sign in and Create account actions

## Navigation Tabs

Header renders navigation tabs below the top bar.

Public visitors see:

1. **Archive** — opens Archive sub-menu:
   - Catalog (`/archive/catalog`)
   - Exclusives (`/archive/exclusives`)
   - Legends Reference (`/archive/legends`)
   - SFBC Reference (`/archive/sfbc`)
   - Timeline (`/archive/timeline`)
2. **Codex** — opens Codex sub-menu:
   - FAQ (`/support`)
   - Guides (`/guides`)
   - User Guide (external link)

Signed-in users additionally see:

1. **Home** (`/home`) — navigates directly
2. **My Library** — opens My Library sub-menu:
   - My Collection (`/collection`)
   - My Events (`/events`)
   - My Reading List (`/reading-list`)
   - My Wishlist (`/wishlist`)
3. **Stats** (`/stats`) inside the Codex sub-menu

The active tab is determined by the current route. The Codex tab is active when the current path is `/support`, `/guides`, or `/stats`.

## Account Menu Workflow

Account menu button shows the user's rank and name. Clicking it opens the account menu. See `account-menu.md` for the full workflow.

## Search Workflow

Header search is documented in `docs/context/workflows/search/header-search.md`.

## Failure Workflow

If token cannot be decoded, authenticated route behavior should clear stored token and redirect to Sign In page through API/auth handling.
