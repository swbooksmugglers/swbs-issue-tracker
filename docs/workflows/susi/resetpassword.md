# Reset Password Workflow

## Rules:

Reset password link must include a token.

Reset password token must have purpose `password_reset`.

Reset password token must not be expired.

New password must meet the following criteria:
- MIN = 8
- MAX = 64
- Must not contain the user's email, email local part, first name, or last name
- Must not match any of the user's last 5 passwords

New password and confirm password must match.

## Client Side Validation

Client side validation occurs when the Reset password page loads and when Reset password button is clicked.

On page load validation failure
1. If token is missing from the URL, display invalid reset link error
2. Do not show the password reset form
3. Do not trigger a backend call for reset password

On form validation failure
1. If new password is < MIN, display error
2. If new password is > MAX, display error
3. If confirm password does not match new password, display error
4. These should not trigger a backend call for reset password

The new password and confirm password fields should allow the user to toggle password visibility.

## Server Side Validation

Server side validation must occur at the API level. This prevents invalid token or password values being used when calling the reset-password API directly.

The reset-password API must validate:
1. Token is present
2. Password is present
3. Password length is between MIN and MAX
4. Token can be decoded
5. Token purpose is `password_reset`
6. Token is not expired
7. Token has not already been used (token `iat` must be after `users.password_reset_at`)
8. Password does not contain the user's email, email local part, first name, or last name
9. Password does not match any of the user's last 5 passwords (returns a generic requirements error, not the specific reuse message)

If invalid parameters are used, return an error and do not update the password.

If the token is invalid, expired, already used, or has the wrong purpose, return an invalid reset link error.

## Success Workflow

1. User clicks reset password link from email
2. App opens Reset password page with token in the URL
3. User enters new password between MIN and MAX characters
4. User enters matching confirm password
5. User clicks Reset password
6. Client calls the reset-password API with token and new password
7. API validates the request
8. API decodes and validates the reset token
9. API hashes the new password
10. API updates the user's password, sets `password_reset_at = NOW()`, clears `password_setup_pending`, and sets `last_signout_at = NOW()` to invalidate existing sessions
11. API returns a success response
12. Client redirects to the Sign In page
13. Sign In page displays password reset success notice

## Failure Workflow

1. If the token is missing, display invalid reset link error
2. If the token is invalid or expired, display the API error message
3. If the password is invalid, display the API error message
4. If the API or network call fails, display a network error message
5. Keep the user on the Reset password page when the form is available
