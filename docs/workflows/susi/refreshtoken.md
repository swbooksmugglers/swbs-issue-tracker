# Refresh Token Workflow

## Rules:

User must have a stored JWT.

Stored JWT must be valid and not expired.

Stored JWT must belong to an enabled user.

Stored JWT must not have been issued at or before the user's last sign out time.

Refresh returns a new bearer JWT.

## Client Side Validation

Client side validation occurs before authenticated API calls made through `apiFetch`.

On client side validation
1. Read the stored JWT
2. If no stored JWT exists, continue the request without an Authorization header
3. If stored JWT exists and is not expiring soon, use it for the API call
4. If stored JWT exists and is expiring soon, call the refresh API before the original API call

## Server Side Validation

Server side validation must occur at the API level.

The refresh API must require a valid bearer token. If the token is missing, invalid, expired, issued for a disabled user, or issued before the user's last sign out time, return an unauthorized error.

## Success Workflow

1. Client prepares an authenticated API request through `apiFetch`
2. Client detects the stored JWT is expiring soon
3. Client calls the refresh API with the current JWT
4. API validates the JWT and resolves the user payload
5. API creates a new JWT using user ID, first name, email, and role from the validated payload
6. API returns the new access token and token type
7. Client stores the refreshed JWT
8. Client sends the original API request with the refreshed JWT

## Failure Workflow

1. If refresh API returns an error, clear the stored JWT
2. Redirect the user to the Sign In page
3. Return an unauthorized response to the original request
4. Do not retry the original request without a valid token

## Token Invalidation

Authenticated endpoints compare the JWT issued-at timestamp against `users.last_signout_at`.

If the JWT was issued at or before the recorded sign out timestamp, the token must be rejected and cannot be refreshed.
