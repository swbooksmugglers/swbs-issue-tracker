# Notifications Workflow Overview

## Purpose

Notifications let admins send short messages to individual users — for example, to inform a user that a requested book has been added to the catalog.

## Role Rules

- Sending a notification requires admin access.
- Viewing and marking notifications read requires a signed-in user.

## Client Entry Points

- Admin sends notifications from the Send Notification dialog in User Management (per-user row action).
- Users view notifications via the bell icon in the header, which opens the notification drawer.

## Server Entry Points

- `POST /api/notification` — admin only; creates a notification for a target user.
- `GET /api/notification` — authenticated user; returns the caller's notifications, unread first.
- `PATCH /api/notification/{id}/read` — authenticated user; marks one notification as read.

## Constraints

- Messages are capped at 280 characters, enforced by the request model and the UI character counter.
- After each new notification is inserted, read notifications beyond the 50-row cap are trimmed for the recipient.
- Unread notifications are protected from the cap trim so users never lose an unseen message.

## Audit Log

Sending a notification records a `create` / `notification` audit log entry with:
- `after_values`: `{ user_id, message, created_by }`

## Failure Handling

- If the send fails, the dialog stays open and a snackbar error is shown.
- If loading notifications fails, the drawer shows no results silently.
- Mark-read failures are non-blocking.
