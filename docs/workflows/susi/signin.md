# Sign In Workflow

## Rules:

Email must be valid email format.

Password must meet the following criteria:
- MIN = 8
- MAX = 64

User account must be activated and enabled before sign in can succeed.

Successful sign in returns a bearer JWT.

## Client Side Validation

Client side validation occurs when Sign In button is clicked.

On client side validation failure
1. If email is empty or not in a valid email format, prevent submission
2. If password is empty, prevent submission
3. These should not trigger a backend call for sign in

The password field should allow the user to toggle password visibility.

## Server Side Validation

Server side validation must occur at the API level. This prevents invalid email or password values being used when calling the signin API directly.

The signin API must validate:
1. Email is present and uses a valid email format
2. Password is present
3. Password length is between MIN and MAX
4. Request ID is present

If invalid parameters are used, return an error and do not attempt credential verification.

If credentials do not match a user record, return an unauthorized error.

If credentials match but the user account is not activated or disabled, return a forbidden error.

## Success Workflow

1. Valid email format must be entered into the Email input
2. Valid password between MIN and MAX characters must be entered
3. Sign In button clicked
4. Client calls the signin API with email, password, and request ID
5. Sign in credentials are validated against the values in the database
6. API verifies the user account is enabled
7. API generates a JWT that is valid for JWT_EXPIRY_MINUTES duration
8. API returns the access token and token type
9. Client stores the JWT
10. Client decodes the JWT to get user navigation state
11. Client loads profile preferences using the JWT
12. Client applies saved theme and display name preferences when available
13. The app redirects to the Home Page

## Failure Workflow

1. If credentials are invalid, display the API error message
2. If the account is not activated, display the account activation error message
3. If the API or network call fails, display a network error message
4. Keep the user on the Sign In page
