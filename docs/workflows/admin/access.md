# Admin Access Workflow

## Rules

Session validation follows `docs/context/auth/session.md`.

Role and permission behavior follows `docs/context/auth/roles.md` and `docs/context/auth/permissions.md`.

Admin page access requires role `admin` or `power_user`.

## Client Side Validation

Client side validation occurs when the Admin page loads.

On client side validation:
1. Read stored token
2. If no token exists, redirect to Sign In page
3. Decode token role
4. If role is not `admin` or `power_user`, redirect to Home page
5. If role is `admin`, render User Management, Archive Import, and Archive Management
6. If role is `power_user`, render Archive Import and Archive Management

## Server Side Validation

Server side validation must occur at the API level.

Admin APIs must enforce the role requirements defined in the permissions matrix.

Authentication and authorization failures follow `docs/context/auth/session.md` and `docs/context/auth/roles.md`.

## Success Workflow

1. Signed-in user opens the account menu
2. User clicks Admin
3. App navigates to the Admin page
4. Admin page validates token and role
5. Admin page renders sections allowed for the user role
6. User can start allowed admin workflows

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If token role is `user`, redirect to Home page
3. If an API authorization check fails, clear stored token and redirect to Sign In page
4. If an API returns forbidden for a role mismatch, do not complete the action
