# Reading List Workflow Overview

## Purpose

My Reading List lets signed-in users track reading state for catalog books independently from owned collection copies.

## Rules

Reading list state is user-owned and scoped by authenticated `user_id`.

Each user may have at most one reading status per catalog book (`user_book_status` unique on `user_id, book_id`).

Reading statuses are:
1. `TBR`
2. `Reading`
3. `Read`
4. `DNF`

Changing a reading status updates `status_updated_at`.

Removing a book from My Collection does not remove it from My Reading List. Removing a book from My Reading List does not remove any collected item.

## Reading Progress

Users may track reading progress on books in `Reading` status.

- If the book has a `page_count`, the user may enter progress as a **page number** or a **percentage**. The dialog shows a Page # / Percent toggle; page mode is the default when page count is available.
- If the book has no `page_count`, only **percentage** input is available.
- A live progress bar previews the entered value before saving.
- Progress is stored on `user_book_status` as `progress_page` (integer) or `progress_pct` (numeric). Exactly one is set per entry; the other is null.
- The Reading tab displays a progress bar per title using whichever field is set.

### Auto-promotion rules

- Setting progress to **100%** (or page = page_count) automatically sets status to **Read**.
- Setting status to **Read** automatically sets progress to 100% (`progress_page = page_count` if known, otherwise `progress_pct = 100`).
- Setting status to **Reading** after a completed Read or DNF (re-read) **resets** progress to null. This reset runs before auto-promote so that prior 100% progress does not immediately flip the status back to Read.

## Reading Sessions

Each time a user reads a book, a `reading_session` row tracks the attempt.

| Event | Session action |
|---|---|
| Status set to Reading (first read or re-read) | Insert new open session (`finished_at = null`, `completed = false`) |
| Status set to Read (or auto-promoted) | Close open session: `finished_at = NOW()`, `completed = true` |
| Status set to DNF | Close open session: `finished_at = NOW()`, `completed = false` |
| Status updated within Reading (progress update) | No session change |
| Status set to TBR | No session change |
| Reading status removed | Sessions preserved as history |

Read count = `COUNT(*) WHERE completed = true` on `reading_session` for a given user + book.

DNF sessions are closed but not counted as reads.

## User Workflow

1. User opens My Library from the authenticated header.
2. User selects My Reading List.
3. Page displays tabs for each reading status with title counts.
4. User filters across all tabs with the global filter.
5. User sorts the active tab by title, author, or status-updated timestamp.
6. User uses the row actions menu to view book details.
7. User uses the row actions menu to update reading status.
8. User uses the row actions menu to remove a book from the reading list after confirmation.
9. Books in the Reading tab display a progress bar if progress has been recorded.

## Add Or Update Workflow

Search Results, Catalog, Exclusives, and My Collection row actions include Add Reading Status or Update reading status.

The dialog:
1. Uses title `Add reading status to <book title>` for new statuses.
2. Uses title `Update reading status for <book title>` for existing statuses.
3. Requires a reading status selected from a MUI Autocomplete.
4. Defaults new statuses to `TBR`.
5. When status is `Reading`, shows a progress section below the status selector.
6. Shows Add or Update first, then Cancel.
7. Shows a snackbar after a successful save.

## Server Entry Points

Reading List APIs are served under `/reading-list`.

APIs must use the authenticated `user_id` from the bearer token and must not accept a user ID from the client.
