# Collection Workflow Overview

## Rules

Collection is available to any signed-in enabled user.

Each user has one default collection named `My Collection`.

Users may add multiple collected items that reference the same book. Notes can distinguish copies.

Users can edit only collected-item fields:
1. Notes
2. Personally signed
3. Reading status
4. Purchase price
5. Date purchased
6. Sold price
7. Date sold
8. Collection customizations

Users cannot edit the referenced book from the Collection page.

## Lookup Data

Reading statuses are stored in `reading_status` and seeded as:
1. `Read`
2. `TBR`
3. `DNF`

Collection customizations are stored in `collection_customization` and seeded as:
1. `Sprayed Pages`
2. `Rebind`
3. `Endpapers`
4. `Case Foiling`

Power users can add and rename collection customizations from Admin lookup management.

## User Workflow

1. User opens Collection from the account menu
2. Client loads the authenticated user's default collection
3. User filters by catalog fields, reading status, signed, customization, purchase date, or text search
4. User opens read-only book details from a row action
5. User edits collected-item fields from a row action
6. User removes a collected item after simple confirmation

Collection add, edit, and remove actions are recorded in the audit log and appear in the user's My Activity list on Home.

## Add From Search Workflow

1. User searches for books from the header
2. User clicks the add-to-collection action on a Search Results row
3. Client calls `POST /api/collection/items` with that row's book id
4. API creates a collected item in the user's default collection
5. Client shows a snackbar with an edit action
6. User can immediately edit collected-item metadata without leaving Search Results

## API Workflow

Collection APIs use the authenticated `user_id` from the bearer token.

User-scoped APIs must not accept a user id from the client.

Adding a collected item defaults reading status to `TBR` when no status is supplied.

Removing a collected item deletes only the user-owned `collected_item` row and its customization links. The referenced book remains unchanged.

Collection mutations record audit log entries:
1. Add uses action `create` and entity type `collected item`
2. Edit uses action `update` and entity type `collected item`
3. Remove uses action `delete` and entity type `collected item`
