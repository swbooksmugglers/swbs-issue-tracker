# Archive Access Workflow

## Rules

User must be signed in with a valid JWT.

User account must be enabled.

## Client Side Validation

Client side validation occurs when Archive page loads.

On client side validation:
1. Read token from route state or stored token
2. If no token exists, redirect to Sign In page
3. If token exists, render Header and Archive content

## Server Side Validation

Server side validation must occur at the API level.

Archive API must validate:
1. Bearer token is present
2. Bearer token is valid and not expired
3. Token belongs to an enabled user
4. Token was issued after the user's last sign out time

If validation fails, return unauthorized.

## Success Workflow

1. User opens account menu
2. User clicks Archive
3. Client navigates to `/archive`
4. Archive page validates token
5. Archive page loads retailer exclusives

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If token is invalid or expired, clear stored token and redirect to Sign In page
