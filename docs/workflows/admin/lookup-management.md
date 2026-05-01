# Lookup Management Workflow

## Rules

Lookup management covers:
1. Authors
2. Publishers
3. Roles
4. Exclusives
5. Eras
6. Series
7. Sub-series

Role and permission behavior follows `docs/context/auth/roles.md` and `docs/context/auth/permissions.md`.

Listing, creating, and renaming lookup records require role `admin` or `power_user`.

Deleting lookup records requires role `admin`.

Lookup names must be unique case-insensitively within the lookup type.

Lookup records referenced by books must not be deleted. Delete conflict handling follows `docs/context/development/database-patterns.md`.

Sub-series records are stored as child series records and are scoped to a parent series. A sub-series cannot be created without a parent series, and its name must be unique within that parent.

Top-level series records support sort order. Eras and sub-series do not expose sort order in lookup management.

## Client Side Validation

Client side validation occurs in lookup management modals, including authors, publishers, roles, exclusives, eras, series, and sub-series.

On search validation failure
1. If query is fewer than 3 characters after trimming whitespace, keep Search button disabled
2. This should not trigger a backend call for lookup search

On add validation failure
1. If name is empty, prevent submission
2. This should not trigger a backend call for lookup create

On edit validation failure
1. If name is empty, prevent submission
2. If name is unchanged case-insensitively, keep Save button disabled

On delete validation failure
1. Delete button remains disabled until confirmation text exactly matches the selected lookup name

Delete controls must only be visible for admins.

## Server Side Validation

Server side validation must occur at the API level.

Lookup APIs must validate:
1. Caller has role `admin` or `power_user` for list, create, and rename
2. Caller has role `admin` for delete
3. Name is present
4. Name is unique case-insensitively
5. Lookup record exists when renaming or deleting
6. Lookup record is not referenced by any book before deleting

If duplicate name exists, return conflict.

If lookup record does not exist, return not found.

If lookup record is referenced by books, apply the block-and-notify rule from `docs/context/development/database-patterns.md`.

## Search Lookup Workflow

1. User opens a lookup management modal
2. User enters search query
3. User clicks Search
4. Client trims query and validates query length is at least 3 characters
5. Client calls the list API for that lookup type
6. Client filters results by query
7. Client displays matching rows

## Add Lookup Workflow

1. User opens lookup modal
2. User clicks Add New
3. User enters name
4. User clicks Save
5. Client calls create API for that lookup type
6. API validates name uniqueness
7. API creates lookup record
8. Client closes modal
9. Client displays success message

## Rename Lookup Workflow

1. User searches for lookup record
2. User clicks edit icon for a search result
3. Client displays edit form
4. User changes name
5. User clicks Save
6. Client calls rename API for that lookup type
7. API validates name uniqueness
8. API renames lookup record
9. Client closes modal
10. Client displays success message

## Delete Lookup Workflow

1. Admin searches for lookup record
2. Admin clicks edit icon for a search result
3. Admin clicks Delete
4. Client opens delete confirmation view
5. Admin types the lookup name
6. Delete button remains disabled until typed name exactly matches selected name
7. Admin clicks Delete
8. Client calls delete API for that lookup type
9. API verifies no books reference the lookup record
10. API applies block-and-notify delete handling from `docs/context/development/database-patterns.md`
11. If deletion is allowed, API deletes lookup record
12. Client closes modal
13. Client displays success message

## Failure Workflow

1. If search fails, display search error message
2. If create fails, keep add view open and display API error message
3. If rename fails, keep edit view open and display API error message
4. If delete is blocked or fails, return to edit view and display API error message
5. If network fails, display network error message
