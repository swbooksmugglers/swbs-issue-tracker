# Archive Access Workflow

## Rules

Public archive pages do not require a valid session.

Account-only archive actions require a valid session. Valid session behavior follows `docs/context/auth/session.md`.

## Client Side Validation

Client side validation occurs when Archive page loads.

On client side validation:
1. Read stored token if present
2. Render Header and public Archive content
3. If an account-only action is requested without a token, prompt the visitor to sign in or create an account

## Server Side Validation

Server side validation must occur at the API level.

Read-only archive APIs allow public access.

Archive mutation and management APIs require session validation following `docs/context/auth/session.md`.

## Success Workflow

1. User opens the Archive navigation menu
2. User clicks Archive
3. Client navigates to an archive route such as `/archive/catalog`
4. Archive page loads public catalog or reference content

## Failure Workflow

1. If a public archive API fails, display the page-specific error state
2. If an account-only request is made without a token, prompt the visitor to sign in or create an account
3. If token is invalid or expired, clear stored token and redirect to Sign In page
