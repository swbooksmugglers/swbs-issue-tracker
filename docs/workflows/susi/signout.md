# Sign Out Workflow

## Rules:

User must be signed in with a valid JWT.

Sign out must invalidate the current and older issued tokens for the user.

## Client Side Validation

Client side validation occurs when the Sign out menu item is clicked.

On client side validation failure
1. If no stored token exists, redirect the user to the Sign In page
2. This should not trigger a backend call for sign out

## Server Side Validation

Server side validation must occur at the API level.

The signout API must require a valid bearer token. If the token is missing, invalid, expired, issued for a disabled user, or issued before the user's last sign out time, return an unauthorized error.

## Success Workflow

1. User opens the account menu from the app header
2. User clicks Sign out
3. Client calls the signout API with the current JWT
4. API validates the JWT and resolves the user ID from the token payload
5. API updates the user's `last_signout_at` value to the current timestamp
6. API returns a success response
7. Client clears the stored JWT
8. The app redirects to the Sign In page

## Failure Workflow

1. If the API returns an unauthorized error, clear the stored JWT and redirect to the Sign In page
2. If the API fails due to a server or network error, keep the user on the current page and display an error message

## Token Invalidation

Authenticated endpoints compare the JWT issued-at timestamp against `users.last_signout_at`.

If the JWT was issued at or before the recorded sign out timestamp, the token must be rejected.
