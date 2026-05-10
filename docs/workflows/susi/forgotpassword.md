# Forgot Password Workflow

## Rules:

Email must be valid email format.

The workflow must not reveal whether an email address is registered.

Password reset email should only be sent when the email belongs to an enabled user account.

Password reset token must expire after `PASSWORD_RESET_EXPIRY_MINUTES` minutes.

## Client Side Validation

Client side validation occurs when Send reset link button is clicked.

On client side validation failure
1. If email is empty or not in a valid email format, prevent submission
2. This should not trigger a backend call for forgot password

## Server Side Validation

Server side validation must occur at the API level. This prevents invalid email values being used when calling the forgot-password API directly.

The forgot-password API must validate:
1. Email is present
2. Email uses a valid email format

If invalid parameters are used, return an error and do not attempt password reset email delivery.

If the email does not belong to a user, return the generic success response.

If the email belongs to a disabled user, return the generic success response and do not send a reset email.

## Success Workflow

1. User clicks Forgot password from the Sign In page
2. User enters valid email format
3. User clicks Send reset link
4. Client calls the forgot-password API with email
5. API validates the request
6. API looks up the user by email
7. If the user exists and is enabled, API generates a password reset token
8. Password reset token has purpose `password_reset`
9. Password reset token expires after `PASSWORD_RESET_EXPIRY_MINUTES` minutes
10. API sends a reset password email containing the reset link
11. API returns a generic success response
12. Client displays the submitted confirmation message
13. User can return to the Sign In page

## Failure Workflow

1. If the email is invalid, display the API error message
2. If the reset email fails to send, display the API error message
3. If the API or network call fails, display a network error message
4. Keep the user on the Forgot password page

## Privacy Workflow

1. The API must return the same generic success message for registered and unregistered emails
2. The API must return the same generic success message for enabled and disabled accounts
3. The client confirmation message must not state whether the email exists
