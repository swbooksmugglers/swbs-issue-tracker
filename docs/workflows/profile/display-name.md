# Display Name Workflow

## Rules

Display name can be set by the signed-in user.

Display name must be at most 100 characters.

Blank display name should clear the saved display name.

When display name is blank or unset, UI should fall back to the user's first name from the JWT.

## Client Side Validation

Client side validation occurs when Save is clicked in the General section.

On client side validation:
1. Limit display name input to 100 characters
2. Trim whitespace before saving
3. Convert blank value to null in the API request

## Server Side Validation

Server side validation must occur at the API level.

The update profile API must validate:
1. Caller is authenticated
2. Display name belongs to the authenticated user
3. Display name is null or a valid string

Profile updates must upsert the profile row when one does not already exist.

## Success Workflow

1. User opens Profile page
2. User enters display name
3. User clicks Save
4. Client trims display name
5. Client calls update profile API with display name or null
6. API validates JWT
7. API creates profile row if needed
8. API updates display name
9. API returns updated profile
10. Client updates display name context
11. Client displays success message
12. Header greeting uses the saved display name

## Clear Display Name Workflow

1. User clears display name field
2. User clicks Save
3. Client sends display name as null
4. API updates display name to null
5. Client falls back to user's first name for display

## Failure Workflow

1. If API returns an error, display failed-to-save display name message
2. If network fails, display network error message
3. Keep the user on the Profile page
