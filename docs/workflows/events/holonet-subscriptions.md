# Holonet Subscriptions Workflow

## Purpose

Holonet subscriptions let users opt in to receive notifications when new public events are posted. Users can subscribe to all events, to specific authors, or both. A digest on the Home page surfaces matching events. A notification nudge appears in the header bell when there is something new to see.

## Subscription Types

| Type | Description |
|---|---|
| **Events** | Notified when any new public event is posted |
| **Author** | Notified when a public event featuring a specific author is posted |

Both types are additive and independent. A user can have events=true with no author subs, author subs with events=false, or both.

## Database Tables

| Table | Purpose |
|---|---|
| `holonet_subscription` | One row per subscribed user. Tracks `events` flag, `events_enabled_at`, `last_seen_at`, and `created_at`. `created_at` is used as a cutoff fallback when `last_seen_at` is NULL. |
| `holonet_author_subscription` | One row per user+author pair. `created_at` marks when the author was added — used to detect newly added authors for retroactive scanning. |

`holonet_subscription` is created on first subscribe action (events or author). A user who only subscribes to authors will have a row with `events = false` — this anchors the `created_at` / `last_seen_at` cutoff for the digest.

## Digest Logic

`GET /api/holonet-subscriptions/digest` returns upcoming public events (`end_date >= CURRENT_DATE`) that match the user's subscriptions since their last dismiss.

The query uses a dynamic WHERE clause with up to three OR branches:

1. **Normal cutoff branch** — events created after `COALESCE(last_seen_at, created_at)` that match the events subscription or authors subscribed before the last dismiss.
2. **Retroactive new-author branch** — all upcoming events featuring authors added after the last dismiss (no date filter). This ensures users see existing events when they add a new author.
3. **Retroactive events-sub branch** — all upcoming events when `events_enabled_at > last_seen_at` (events was recently enabled). No date filter.

**Key invariant:** Existing subscriptions (added before `last_seen_at`) never trigger a retroactive scan — only newly added subscriptions do.

## Notification Nudge

The `notification` table is used to drive the header bell badge count. A nudge with the message `"You have new Holonet updates — see Holonet Updates on your home page"` is created in two situations:

1. **New public event posted** — `PublicEventService._notify_subscribers` is called. It notifies:
   - All users with `holonet_subscription.events = true` (excluding the submitter)
   - All users in `holonet_author_subscription` for any author on the event (excluding the submitter)
2. **User subscribes to events or an author** — `HolonetSubscriptionService._maybe_notify` checks if any upcoming matching events exist and creates a notification if none is already pending (unread).

Both paths deduplicate: if the user already has an unread holonet nudge, no second notification is created.

## Dismiss

`POST /api/holonet-subscriptions/digest/dismiss` sets `last_seen_at = NOW()`. On next digest load, all subscription dates will predate `last_seen_at`, so only events posted after the dismiss will appear.

## API Endpoints

All endpoints require authentication.

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/holonet-subscriptions` | Get current subscriptions |
| `POST` | `/api/holonet-subscriptions/events` | Subscribe to all events |
| `DELETE` | `/api/holonet-subscriptions/events` | Unsubscribe from all events |
| `POST` | `/api/holonet-subscriptions/authors/{author_id}` | Subscribe to an author |
| `DELETE` | `/api/holonet-subscriptions/authors/{author_id}` | Unsubscribe from an author |
| `GET` | `/api/holonet-subscriptions/digest` | Get digest of matching upcoming events |
| `POST` | `/api/holonet-subscriptions/digest/dismiss` | Mark digest as seen |

## Frontend

- `client/src/components/HolonetSubscriptionForm.tsx` — subscription toggle form; makes individual API calls per toggle/add/remove (not a batch save)
- `client/src/components/modals/HolonetSubscriptionsModal.tsx` — wraps the form in a Dialog
- `client/src/pages/Home.tsx` — fetches subscriptions then digest on load; renders the "Holonet Updates" section with a badge showing event count; handles dismiss, add-to-my-events, and remove-from-my-events actions inline
- `client/src/components/Header.tsx` — fetches `/api/notification` on mount and shows the unread count badge on the bell icon

## Schema Notes

- `events_enabled_at` was added in migration `20260701_holonet_subscription_events_enabled_at.sql`. Backfilled to `created_at` for existing event subscribers. `NULL` for author-only subscribers and users who have unsubscribed from events.
- When `events` is set to `false`, `events_enabled_at` is cleared to `NULL` so a future re-enable gets a fresh timestamp.
