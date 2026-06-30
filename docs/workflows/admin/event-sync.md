# Event Sync Workflow

Event Sync requires role `admin`. Unlike Archive Import and Archive Management, Event Sync is not available to `power_user`.

Event Sync covers two independent convention integrations that share one job table and one concurrency lock:

1. **Fan Expo Sync** — scrapes upcoming Fan Expo events and comic creator guest lists.
2. **GalaxyCon Sync** — scrapes upcoming GalaxyCon events and creator appearance pages.

Each sync starts a persisted background job. It scrapes the provider's public site, creates any `public_event` rows that do not already exist (matched by name + date), and links any scraped creators who match a known local `author` to the event as comic creator guests. Existing events and existing author links are skipped, not duplicated.

## Shared Concurrency Lock

Fan Expo and GalaxyCon jobs are stored in a single `event_sync_job` table distinguished by a `source` column (`fanexpo` or `galaxycon`). Only one event sync job — from either source — may be queued or running at a time. Starting one provider's sync while the other is active returns `409 Conflict`.

## Job Progress

The job records:
- `processed` / `total` — scrape progress through events (and, for GalaxyCon, creator pages).
- `events_created` / `events_skipped` — new vs. already-existing public events.
- `author_links_linked` / `author_links_skipped` — creator-to-event author links made vs. skipped (already linked, or no matching local author).
- `failed` — per-event or per-creator scrape failures that did not stop the job.
- `created_events` — array of newly created event names, displayed as a list under the progress alert.

Event sync jobs store the initiating user ID and email snapshot. The Admin progress indicator persists the latest job per source and shows the last run timestamp plus initiating email after the job completes or stops.

## Client Entry Point

Fan Expo Sync and GalaxyCon Sync appear as separate cards under the Admin page's Event Management section, each with its own button, progress bar, and status alert.

## Server Entry Points

- Fan Expo Sync APIs are served under `/api/fanexpo/events/sync` (not under `/admin`).
- GalaxyCon Sync APIs are served under `/api/galaxycon/events/sync` (not under `/admin`).

All Event Sync job APIs (start, current, by ID) require `admin` access. The underlying read-only scrape endpoints (`/events`, `/creators`, etc.) on each router require only `power_user`.

When `SCHEDULER_ENABLED=true`, Event Sync also runs daily at `EVENT_SYNC_SCHEDULE_TIME` in `SCHEDULER_TIMEZONE`. The scheduler runs Fan Expo first, then GalaxyCon, sequentially — never in parallel, consistent with the shared lock. Scheduled runs use `SCHEDULER_EMAIL` for audit log identity and do not authenticate as a user. If a provider raises during the scheduled run, the error is logged and the scheduler still attempts the next provider.

## Failure Handling

Individual event or creator scrape failures do not stop the full job. The failure is recorded and the job continues to the next candidate.

If the provider's site scrape fails entirely (e.g. site structure changed, network error), the job stops and is marked failed with the scrape error message.

If the job stops unexpectedly, the persisted job status is set to `failed` and the Admin page displays the failure state.

Manual sync requests return `409 Conflict` when a non-stale event sync job (either source) is already queued or running. Stale active jobs older than `SCHEDULER_TIMEOUT_MINUTES` are marked failed before a new manual or scheduled run starts.
