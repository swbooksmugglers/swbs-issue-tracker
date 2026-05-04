# Profile Access Workflow

## Rules

Profile access requires a valid session.

Valid session behavior follows `docs/context/auth/session.md`.

Profile data belongs to the authenticated user resolved from the JWT.

If no profile row exists, default profile values must be returned.

## Client Side Validation

Client side validation occurs when the Profile page loads.

On client side validation:
1. Read stored token
2. If no token exists, redirect to Sign In page
3. Decode token to get the user's first name
4. Render Profile page with Header
5. Use display name from profile context when available
6. Fall back to first name when display name is not set

## Server Side Validation

Server side validation must occur at the API level.

Profile API session validation follows `docs/context/auth/session.md`.

## Get Profile Workflow

1. Client calls profile API with current JWT
2. API validates JWT
3. API resolves user ID from token payload
4. API looks up profile by user ID
5. If profile exists, return saved profile values
6. If profile does not exist, return default profile values

Default profile values are:
1. Theme: `default`
2. Colour mode: `auto`
3. Display name: null

## Failure Workflow

1. If no token exists, redirect to Sign In page
2. If token is invalid or expired, clear stored token and redirect to Sign In page
3. If profile loading fails, keep existing client defaults and display an error where the workflow requires it
