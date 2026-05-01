# Change Password Workflow

## Rules

User must be signed in.

Current password is required.

New password must meet the following criteria:
- MIN = 8
- MAX = 64
- Must not contain the user's email, email local part, first name, or last name

New password must be different from current password.

New password and confirm new password must match.

## Client Side Validation

Client side validation occurs when Change password button is clicked.

On client side validation failure
1. If current password is empty, keep Change password button disabled
2. If new password is empty, keep Change password button disabled
3. If confirm new password is empty, keep Change password button disabled
4. If new password equals current password, display error
5. If new password and confirm new password do not match, display error
6. If new password is < MIN, display error
7. If new password is > MAX, display error
8. These should not trigger a backend call for change password

The current password, new password, and confirm new password fields should allow the user to toggle password visibility.

## Server Side Validation

Server side validation must occur at the API level.

The change password API must validate:
1. Caller is authenticated
2. Current password is present
3. New password is present
4. New password length is between MIN and MAX
5. Current password matches the stored password hash
6. New password does not contain the user's email, email local part, first name, or last name

If current password is incorrect, return unauthorized.

If new password is invalid, return an error and do not update the password.

## Success Workflow

1. User opens Profile page
2. User enters current password
3. User enters new password
4. User enters matching confirm new password
5. User clicks Change password
6. Client validates password fields
7. Client calls change password API
8. API validates JWT
9. API verifies current password against stored hash
10. API hashes new password
11. API updates user password
12. API returns success response
13. Client clears password fields
14. Client displays success message

## Failure Workflow

1. If current password is incorrect, display current-password error
2. If new password is invalid, display password validation error
3. If API returns an error, display failed-to-change password message
4. If network fails, display network error message
5. Keep the user on the Profile page
