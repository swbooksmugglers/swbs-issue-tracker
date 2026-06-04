# Wishlist Workflow Overview

## Purpose

My Wishlist lets signed-in users track catalog books they want to acquire, independently from their collection.

## Rules

Wishlist state is user-owned and scoped by authenticated `user_id`. No admin or cross-user access.

Each user may have at most one wishlist entry per catalog book (enforced by a unique constraint on `wishlist_item(user_id, book_id)`).

Adding a book already in the user's collection to the wishlist is rejected with a 409 response.

Adding a book to the user's collection automatically removes it from the wishlist in the same transaction.

## User Workflow

1. User opens My Library from the authenticated header.
2. User selects My Wishlist.
3. Page displays all wishlist books sorted by Date Added (newest first) by default.
4. User filters books using the local search field and the filter dialog.
5. User sorts by Date Added, title, author, timeline, era, continuity, format, or ISBN.
6. User uses the row actions menu to view book details.
7. User uses the row actions menu to add a book to their collection (removes it from the wishlist).
8. User uses the row actions menu to remove a book from the wishlist after confirmation.

## Add To Wishlist Workflow

Add to wishlist is available from Search Results row actions.

See `docs/context/workflows/search/results.md` for the full workflow including the Add to Wishlist / View in Wishlist toggle behaviour.

## Server Entry Points

Wishlist APIs are served under `/wishlist`.

APIs must use the authenticated `user_id` from the bearer token and must not accept a user ID from the client.
