# Announcements Workflow

Announcements requires role `admin`. Viewing and dismissing the active announcement only requires authentication (any signed-in user).

Announcements is a single rolling banner shown on the Home page. There is no separate "active" flag — the active announcement is simply the `announcement` row with the highest `id`. Publishing a new announcement supersedes the previous one; it does not edit or delete it.

Publishing a new announcement resets every user's dismissal. After publish, all users see the new banner again even if they had dismissed the previous one.

## Validation

The announcement body:
1. Must not be empty after trimming whitespace.
2. Must not exceed 1000 characters.

Validation runs both client-side (character counter, disabled Publish button) and server-side (`CreateAnnouncementRequest`).

## Client Entry Point

Announcements appears in the Admin page System Management section. The editor is a Markdown live-preview editor (`MDEditor`). Publishing requires confirming a dialog before the request is sent.

On the Home page, the active announcement renders as a dismissible banner if the signed-in user has not already dismissed that specific announcement ID.

## Server Entry Points

Announcement APIs are served under `/announcement` (not `/admin`):
- `GET /api/announcement` — get the active announcement and whether the calling user has dismissed it. Requires authentication only.
- `POST /api/announcement` — publish a new announcement. Requires `admin`.
- `POST /api/announcement/dismiss` — dismiss an announcement ID for the calling user. Requires authentication only.

## Failure Handling

If publish validation fails (empty or over 1000 characters), return `400 Bad Request` with the validation message.

If an unexpected database error occurs, return `500 Internal Server Error` with a generic message.

## Database

- `announcement`: `id`, `body`, `created_by` (FK to users), timestamps. Active announcement = highest `id`.
- `profile.dismissed_announcement_id`: per-user pointer to the last announcement ID that user dismissed. A user's profile row is created on first dismiss if it does not already exist.
