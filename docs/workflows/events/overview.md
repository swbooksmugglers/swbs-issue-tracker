# Events Workflow Overview

## Purpose

My Events lets signed-in users record and manage book-signing events they attend or plan to attend. An event captures the authors present, the books they brought (from the user's collection), and the individual signing sessions within that event.

## Domain Concepts

- **Event**: Top-level record owned by a user. Has a name, date range, optional location, and optional notes.
- **Event author**: An author who attended the event, linked via `event_author`.
- **Event item**: A collected item the user brought to the event, linked via `event_item`.
- **Session**: A discrete signing opportunity within the event (a specific date, time, and optional location). Stored in `event_session`.
- **Session author**: An author attending a particular session, linked via `event_session_author`.
- **Session item**: A collected item signed or present in a specific session, linked via `event_session_item`.

## Role Rules

My Events is available to any signed-in enabled user.

Events are user-scoped: each user sees and manages only their own events.

## User Workflow

### Event List

1. User opens My Events from the My Library nav menu.
2. Client loads the authenticated user's events, ordered by start date descending.
3. Each row shows name, date range, location, and counts of authors, books, and sessions.
4. User expands an event to see its full detail.

### Create Event

1. User clicks "Create Event".
2. User fills in name (required), start date (required), end date (required), location (optional), notes (optional).
3. Client calls `POST /api/events`.
4. New event appears in the list.

### Edit and Delete Event

- User uses the event's action menu to edit name, dates, location, or notes.
- User uses the action menu to delete the event. Deleting an event cascades to all event authors, event items, sessions, session authors, and session items.

### Authors

- User adds an author to the event by selecting from the full author list (loaded via `GET /api/events/authors`).
- User removes an author from the event via a chip delete on the author chip.

**Remove author from event — deletion rules:**

| Condition | Behavior |
|---|---|
| Author is still in any session | Hard block (409 `conflict`). Must remove the author from all sessions first. Response includes the blocking session list. |
| Author has sole-authored books at the event (no co-author also attending the event) | Soft block (409 `confirmation_required`). Response includes the book list. Re-submit with `?force=true` to proceed. |
| `force=true` submitted | Sole-authored books are deleted from the event (cascades to sessions); then the author is removed. |
| Author has co-authored books where the co-author is still at the event | Those books are not removed. |

### Books (Event Items)

- User adds a book to the event by selecting from their collection (collected items).
- User removes a book from the event via the book's remove action.

**Remove book from event — deletion rules:**

| Condition | Behavior |
|---|---|
| Book is assigned to one or more sessions | Hard block (409 `conflict`). Response includes `session_count`. Must remove from each session first. |
| Book is not in any session | Deleted immediately. |

### Sessions

A session represents a specific signing slot within the event (e.g. an author appearing at a particular time and location).

- User creates a session by specifying date (required), time (required), location (optional), notes (optional), and an `attended` flag (default false).
- Sessions can have their own author and book sub-lists, which must be subsets of the event-level lists.
- User marks a session as attended using the `attended` toggle.

**Remove author from session — deletion rules:**

| Condition | Behavior |
|---|---|
| Author has sole-authored books in that session (no co-author also in the session) | Soft block (409 `confirmation_required`). Response includes the book list. Re-submit with `?force=true` to proceed. |
| `force=true` submitted | Sole-authored books are removed from the session; then the author is removed. |
| Author has co-authored books where the co-author is still in the session | Those books are not removed. |

**Remove book from session:**

Removing a book from a session always succeeds immediately (leaf node with no downstream dependencies).

## API Endpoints

All events endpoints require authentication. User scope is read from the bearer token — client never sends a `user_id`.

| Method | Path | Purpose |
|---|---|---|
| `GET` | `/api/events/authors` | List all authors |
| `GET` | `/api/events` | List authenticated user's events |
| `POST` | `/api/events` | Create an event |
| `GET` | `/api/events/{event_id}` | Get full event detail |
| `PUT` | `/api/events/{event_id}` | Update event |
| `DELETE` | `/api/events/{event_id}` | Delete event |
| `DELETE` | `/api/events/{event_id}/authors/{author_id}` | Remove author from event |
| `POST` | `/api/events/{event_id}/items` | Add collected item to event |
| `DELETE` | `/api/events/{event_id}/items/{event_item_id}` | Remove book from event |
| `POST` | `/api/events/{event_id}/sessions` | Create session |
| `PUT` | `/api/events/{event_id}/sessions/{session_id}` | Update session |
| `DELETE` | `/api/events/{event_id}/sessions/{session_id}` | Delete session |
| `DELETE` | `/api/events/{event_id}/sessions/{session_id}/authors/{author_id}` | Remove author from session |
| `DELETE` | `/api/events/{event_id}/sessions/{session_id}/items/{event_item_id}` | Remove book from session |

## Block-and-Notify Response Shapes

Hard block responses use `"conflict"`:
```json
{ "message": "...", "conflict": { ... } }
```

Soft block (confirmation required) responses use `"confirmation_required"`:
```json
{ "message": "...", "confirmation_required": { "books": [...] } }
```

Both shapes return HTTP 409. Hard blocks require the dependency to be manually resolved first. Soft blocks allow the client to re-submit with `?force=true` after the user confirms.

## Database Tables

| Table | Purpose |
|---|---|
| `event` | Top-level event record (user-owned) |
| `event_author` | Authors attending the event |
| `event_item` | Collected items brought to the event |
| `event_session` | Individual signing sessions within the event |
| `event_session_author` | Authors attending a specific session |
| `event_session_item` | Books present in a specific session |

`event_item.id` has `ON DELETE CASCADE` from `event`. `event_session_item.event_item_id` has `ON DELETE CASCADE` from `event_item`, so deleting an event item automatically cleans up its session assignments.
