# User Management Workflow

## Rules

User Management requires role `admin`.

Role and permission behavior follows `docs/context/auth/roles.md` and `docs/context/auth/permissions.md`.

The protected admin account must not be modified or deleted.

Protected admin behavior follows `docs/context/auth/protected-admin.md`.

## Client Side Validation

Client side validation occurs when the Admin page loads for an admin user.

On client side validation:
1. If user role is not `admin`, do not render User Management
2. If user role is `admin`, call the users API
3. Display loading state while users are loading
4. Display no-users state when the users API returns an empty list
5. Display users in a paginated table

The users table supports:
1. Search by first name
2. Search by last name
3. Search by email
4. Pagination with 5 rows per page

User search is inactive until the search text is at least 3 characters after trimming whitespace.

Protected users must have role, enabled toggle, and delete controls disabled.

## Server Side Validation

Server side validation must occur at the API level.

User Management APIs must validate:
1. Caller has role `admin`
2. Target user exists when updating or deleting
3. Target user is not the protected admin account
4. Update request includes at least one field to update

If update request includes no fields, return a bad request error.

If target user does not exist, return not found.

If target user is protected, return forbidden.

## List Users Workflow

1. Admin page loads for admin user
2. Client calls users API
3. API lists users ordered by ID
4. API marks the protected admin account when configured
5. Client renders user table

## Update User Role Workflow

1. Admin changes a user's role from the role selector
2. Client calls update user API with role
3. API validates admin access
4. API validates target user is not protected
5. API updates user role
6. Client updates the user row locally
7. Client displays success message

Valid roles are:
1. `admin`
2. `power_user`
3. `user`

## Enable Or Disable User Workflow

1. Admin toggles the Enabled switch
2. Client calls update user API with enabled value
3. API validates admin access
4. API validates target user is not protected
5. API updates enabled status
6. Client updates the user row locally
7. Client displays success message

## Delete User Workflow

1. Admin clicks delete icon for a user
2. Client opens delete confirmation dialog
3. Admin types the user's email address
4. Delete button remains disabled until typed email exactly matches target email
5. Admin clicks Delete
6. Client calls delete user API
7. API validates admin access
8. API validates target user is not protected
9. API applies block-and-notify delete handling from `docs/context/development/database-patterns.md`
10. If deletion is allowed, API deletes user
11. Client removes user from local table
12. Client displays success message

## Failure Workflow

1. If loading users fails, display failed-to-load message
2. If update fails, keep local user state unchanged and display API error message
3. If delete is blocked or fails, close confirmation dialog and display API error message
4. If network fails, display network error message
