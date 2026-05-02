# My Activity Workflow

## Purpose

My Activity gives signed-in users a compact view of their own recent audited actions.

## Access

Any signed-in user can view their own activity.

Users must not view another user's activity.

Admins view system-wide audit history through Admin Audit Log, not through My Activity.

## Client Flow

1. User opens Home.
2. Client calls `/api/profile/activity`.
3. Home displays the user's 10 most recent activity entries.
4. Empty, loading, and error states are displayed in place.

## Server Flow

Activity API is served under `/profile/activity`.

The endpoint must use `require_auth`.

The endpoint must pass `payload["user_id"]` to the audit log query.

The endpoint must not accept a user ID from the client.

The endpoint always returns at most the 10 most recent entries and does not expose client-controlled pagination.

## Display Fields

My Activity displays:

1. Date
2. Action
3. Entity
4. Status
5. Message

Before and after JSON snapshots are not displayed in My Activity.

Collection add, edit, and remove actions appear in My Activity when recorded as audit log entries for the current user.
