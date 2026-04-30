# Archive Access Workflow

## Rules

Archive access requires a valid session.

Valid session behavior follows `docs/context/auth/session.md`.

## Client Side Validation

Client side validation occurs when Archive page loads.

On client side validation:
1. Read token from route state or stored token
2. If no token exists, redirect to Sign In page
3. If token exists, render Header and Archive content

## Server Side Validation

Server side validation must occur at the API level.

Archive API session validation follows `docs/context/auth/session.md`.

## Success Workflow

1. User opens account menu
2. User clicks Archive
3. Client navigates to `/archive`
4. Archive page validates token
5. Archive page loads exclusives

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If token is invalid or expired, clear stored token and redirect to Sign In page
