# Activate Account Workflow

## Rules:

Activation link must include a token.

Activation token must have purpose `activation`.

Activation token must not be expired.

Activation token expires after 24 hours.

Only activated accounts can sign in.

## Client Side Validation

Client side validation occurs when the Account activation page loads.

On client side validation failure
1. If token is missing from the URL, display invalid activation link error
2. Do not trigger a backend call for activation

## Server Side Validation

Server side validation must occur at the API level.

The activate API must validate:
1. Token is present
2. Token can be decoded
3. Token purpose is `activation`
4. Token is not expired

If the token is invalid, expired, or has the wrong purpose, return an activation error and do not enable the user account.

## Success Workflow

1. User clicks activation link from signup email
2. App opens Account activation page with token in the URL
3. Client calls the activate API with token
4. API decodes and validates the activation token
5. API resolves the user ID from the token payload
6. API enables the user account
7. API returns success response
8. Client displays account activated message
9. User can navigate to the Sign In page

## Failure Workflow

1. If token is missing, display invalid activation link error
2. If token is invalid or expired, display the API error message
3. If token has the wrong purpose, display the API error message
4. If the API or network call fails, display a network error message
5. Keep the user on the Account activation page with a link back to Sign In
