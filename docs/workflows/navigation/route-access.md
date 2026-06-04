# Route Access Workflow

## Rules

Authenticated routes must require a stored token.

If no token exists, redirect to Sign In page.

Unknown routes redirect to Sign In page.

## Client Side Validation

Client side validation occurs when protected pages load.

Protected pages read the stored token.

If no stored token exists, protected page redirects to Sign In page.

## Protected Routes

Protected routes include:
1. `/home`
2. `/settings`
3. `/archive/catalog`
4. `/archive/exclusives`
5. `/archive/legends`
6. `/archive/sfbc`
7. `/archive/timeline`
8. `/collection`
9. `/events`
10. `/reading-list`
11. `/wishlist`
12. `/admin`
13. `/search`
14. `/support`
15. `/gutter-codes`
16. `/sfbc-guide`

Admin-only data views inside `/admin` include:
1. Audit Log
2. User Management

SUSI public routes include:
1. `/`
2. `/signup`
3. `/activate`
4. `/forgot-password`
5. `/reset-password`

## Admin Route Workflow

Admin route has additional role validation.

If role is not `admin` or `power_user`, redirect to Home page.

## Unknown Route Workflow

1. User navigates to an unknown route
2. App redirects to Sign In page

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If API later rejects token, clear stored token and redirect to Sign In page
3. If the stored token expires while the user is idle, clear stored token and redirect to Sign In page
