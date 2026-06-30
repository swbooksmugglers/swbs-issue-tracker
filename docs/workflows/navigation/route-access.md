# Route Access Workflow

## Rules

Private routes must require a stored token.

If no token exists on a private route, redirect to Sign In page.

Unknown routes redirect to the public catalog.

## Client Side Validation

Client side validation occurs when protected pages load.

Protected pages read the stored token.

If no stored token exists, protected page redirects to Sign In page.

Public archive and Codex reference pages render without a stored token. Account-only actions on public pages prompt visitors to sign in or create an account.

## Public Routes

Public routes include:
1. `/`
2. `/signin`
3. `/signup`
4. `/activate`
5. `/forgot-password`
6. `/reset-password`
7. `/archive/catalog`
8. `/archive/exclusives`
9. `/archive/sfbc`
10. `/archive/timeline`
11. `/archive/legends`
12. `/search`
13. `/support`
14. `/guides`

## Protected Routes

Protected routes include:
1. `/home`
2. `/settings`
3. `/collection`
4. `/events`
5. `/reading-list`
6. `/wishlist`
7. `/admin`
8. `/stats`

Admin-only data views inside `/admin` include:
1. Audit Log
2. User Management

The public landing route `/` redirects to `/archive/catalog`.

## Admin Route Workflow

Admin route has additional role validation.

If role is not `admin` or `power_user`, redirect to Home page.

## Unknown Route Workflow

1. User navigates to an unknown route
2. App redirects to the public catalog

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If API later rejects token, clear stored token and redirect to Sign In page
3. If the stored token expires while the user is idle, clear stored token and redirect to Sign In page
