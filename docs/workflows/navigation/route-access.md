# Route Access Workflow

## Rules

Authenticated routes must require a stored or route-state token.

If no token exists, redirect to Sign In page.

Unknown routes redirect to Sign In page.

## Client Side Validation

Client side validation occurs when protected pages load.

Protected pages read token from:
1. Route state
2. Stored token

If neither source contains a token, protected page redirects to Sign In page.

## Protected Routes

Protected routes include:
1. `/home`
2. `/profile`
3. `/archive`
4. `/collection`
5. `/admin`
6. `/search`

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
