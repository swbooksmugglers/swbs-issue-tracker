# Reading List Workflow Overview

## Purpose

My Reading List lets signed-in users track reading state for catalog books independently from owned collection copies.

## Rules

Reading list state is user-owned and scoped by authenticated `user_id`.

Each user may have at most one reading status per catalog book.

Reading statuses are:
1. `TBR`
2. `Reading`
3. `Read`
4. `DNF`

Changing a reading status updates `status_updated_at`.

Removing a book from My Collection does not remove it from My Reading List. Removing a book from My Reading List does not remove any collected item.

## User Workflow

1. User opens My Library from the authenticated header.
2. User selects My Reading List.
3. Page displays tabs for each reading status with title counts.
4. User filters across all tabs with the global filter.
5. User sorts the active tab by title, author, or status-updated timestamp.
6. User uses the row actions menu to view book details.
7. User uses the row actions menu to update reading status.
8. User uses the row actions menu to remove a book from the reading list after confirmation.

## Add Or Update Workflow

Search Results, Catalog, Exclusives, and My Collection row actions include Add Reading Status or Update reading status.

The dialog:
1. Uses title `Add reading status to <book title>` for new statuses.
2. Uses title `Update reading status for <book title>` for existing statuses.
3. Requires a reading status selected from a MUI Autocomplete.
4. Defaults new statuses to `TBR`.
5. Shows Add or Update first, then Cancel.
6. Shows a snackbar after a successful save.

## Server Entry Points

Reading List APIs are served under `/reading-list`.

APIs must use the authenticated `user_id` from the bearer token and must not accept a user ID from the client.
