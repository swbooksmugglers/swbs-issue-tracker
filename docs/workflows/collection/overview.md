# Collection Workflow Overview

## Rules

Collection is available to any signed-in enabled user.

Each user has one default collection named `My Collection`.

Users may add multiple collected items that reference the same book. Notes can distinguish copies.

Users can edit only collected-item fields:
1. Notes
2. Signed status
3. Purchase price
4. Date purchased
5. Sold price
6. Date sold
7. Collection customizations
8. My Printing
9. Legends Banner (Mass Market Paperbacks only; forced true and non-editable for books in `legends_banner_only`)
10. Contributor signatures (which book contributors have signed this specific copy)

Reading status is user-book state managed through My Reading List. Collection rows can show and update reading status, but the status is not owned by the collected copy.

Users cannot edit the referenced book from the Collection page.

## Lookup Data

Reading statuses are stored in `reading_status` and seeded as:
1. `TBR`
2. `Reading`
3. `Read`
4. `DNF`

Collection customizations are stored in `customization` and seeded as:
1. `Sprayed Pages`
2. `Rebind`
3. `Endpapers`
4. `Case Foiling`

Power users can add and rename collection customizations from Admin lookup management.

Signed types are tracked per contributor and stored in `collected_item_contributor_signed_type`. Available types are seeded in `collected_item_signed_type` as:
1. `Signature`
2. `Personalized`
3. `Bookplate`
4. `Dated`
5. `Inscription`

An item is considered signed (`is_signed = true`) when at least one contributor has one or more types assigned.

## User Workflow

1. User opens Collection from the account menu
2. Client loads the authenticated user's default collection
3. User filters by catalog fields, reading status, signed types, customization, purchase date, Legends Banner, or text search (filter button shown when results exist or when filters are active)
4. User sorts the table by title, author, reading status, format, publication date, or date added using column headers
5. User uses the row actions menu to open read-only book details
6. User uses the row actions menu to edit collected-item fields
7. User uses the row actions menu to remove a collected item after simple confirmation

Collection add, edit, and remove actions are recorded in the audit log and appear in the user's My Activity list on Home.

## Add From Search Workflow

1. User searches for books from the header
2. User clicks the add-to-collection action on a Search Results row
3. Client checks `GET /api/collection/items/book/{book_id}/count` for existing copies in the authenticated user's collection
4. If existing copies are found, the client asks the user to confirm adding another copy
5. Client calls `POST /api/collection/items` with that row's book id
6. API creates a collected item in the user's default collection
7. Client shows a snackbar with an edit action
8. User can immediately edit collected-item metadata without leaving Search Results

## API Workflow

Collection APIs use the authenticated `user_id` from the bearer token.

User-scoped APIs must not accept a user id from the client.

Adding a collected item does not set reading status unless the request explicitly includes a `reading_status_id`.

Duplicate copies of the same book are allowed. User-facing add flows should confirm before adding another copy when the same `book_id` already exists in the authenticated user's collection.

Removing a collected item deletes only the user-owned `collected_item` row and its customization links. The referenced book remains unchanged.

Collection mutations record audit log entries:
1. Add uses action `create` and entity type `collected item`
2. Edit uses action `update` and entity type `collected item`
3. Remove uses action `delete` and entity type `collected item`
