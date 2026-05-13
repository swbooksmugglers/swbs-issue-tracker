# Header Workflow

## Rules

Header reads the stored token directly.

Header displays app name.

Header displays global search.

Header displays account menu button.

Account menu button displays the user's current rank based on their faction and collected item count.

## Client Side Validation

Client side validation occurs when Header renders.

On render:
1. Decode token payload
2. Read first name and role from token
3. Read display name, faction, and collected item count from theme preference context
4. Compute rank from faction and collected item count
5. Display rank and name as the account menu button label (e.g. "Padawan Herm")
6. Use display name when set, fall back to first name from token

## Navigation Workflow

1. User clicks app name
2. Client navigates to Home page
3. User opens Archive menu from the tab row to access Catalog, Exclusives, Legends Reference, and Timeline
4. User opens My Library menu from the tab row to access My Collection and My Reading List

## Search Workflow

Header search is documented in `docs/context/workflows/search/header-search.md`.

## Failure Workflow

If token cannot be decoded, authenticated route behavior should clear stored token and redirect to Sign In page through API/auth handling.
