# Sign Up Workflow

## Rules:

First name is required.

Last name is required.

Email must be valid email format.

Password must meet the following criteria:
- MIN = 8
- MAX = 64
- Must not contain the user's email, email local part, first name, or last name

Password and confirm password must match.

New accounts must be created disabled until the user activates the account from email.

Email must be unique.

## Client Side Validation

Client side validation occurs when Create account button is clicked.

On client side validation failure
1. If first name is empty, prevent submission
2. If last name is empty, prevent submission
3. If email is empty or not in a valid email format, prevent submission
4. If password is < MIN, display error
5. If password is > MAX, display error
6. If confirm password does not match password, display error
7. These should not trigger a backend call for sign up

The password and confirm password fields should allow the user to toggle password visibility.

## Server Side Validation

Server side validation must occur at the API level. This prevents invalid user details being used when calling the signup API directly.

The signup API must validate:
1. First name is present
2. Last name is present
3. Email is present and uses a valid email format
4. Password is present
5. Password length is between MIN and MAX
6. Password does not contain the user's email, email local part, first name, or last name

If invalid parameters are used, return an error and do not create a user.

If the email already exists, return a conflict error and do not create a duplicate user.

## Success Workflow

1. User enters first name
2. User enters last name
3. User enters valid email format
4. User enters password between MIN and MAX characters
5. User enters matching confirm password
6. User clicks Create account
7. Client calls the signup API with first name, last name, email, and password
8. API validates the signup request
9. API hashes the password
10. API creates a user with role `user` and `enabled` set to false
11. API generates an activation token that expires in 24 hours
12. API sends an activation email containing the activation link
13. API returns a created response
14. Client displays the Check your email confirmation page
15. User clicks the activation link from email
16. Activation API validates the activation token
17. Activation API enables the user account
18. User can return to the Sign In page and sign in

## Failure Workflow

1. If the email already exists, display the API error message
2. If the signup request is invalid, display the API error message
3. If the activation email fails to send, display the API error message
4. If the API or network call fails, display a network error message
5. Keep the user on the Create account page

## Activation Workflow

1. Activation link must include an activation token
2. Activation token must have purpose `activation`
3. Activation token must not be expired
4. If the token is valid, enable the user account
5. If the token is missing, invalid, expired, or has the wrong purpose, display an activation error
