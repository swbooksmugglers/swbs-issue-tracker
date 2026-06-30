# ASIN Sync Workflow

ASIN Sync requires role `admin` or `power_user`.

ASIN Sync starts a persisted background job. It scans local books that have no ASIN, deduplicates by normalized ISBN, skips ISBNs whose latest attempt is still inside its retry window, looks up Amazon by each remaining ISBN through ScraperAPI, and saves the returned ASIN to every local book sharing that ISBN when one is found.

Unlike Summary Sync, ASIN Sync never copies a value between different books. ASIN identifies a specific Amazon listing and is edition-specific, so each ASIN is only ever looked up externally for the exact ISBN it is saved against.

ASIN attempt retry windows:
1. `not_found` retries after 90 days.
2. `failed` retries after 24 hours.
3. `rate_limited` retries after 24 hours and stops the job immediately.

Missing Amazon results and individual transient lookup failures do not stop the batch. The job records checked, updated, unavailable (`not_found`), failed, and skipped counts for the Admin progress display. Backend requests to ScraperAPI are spaced by `ASIN_SYNC_REQUEST_DELAY_SECONDS`.

ASIN Sync jobs store the initiating user ID and email snapshot. The Admin progress indicator persists the latest job and shows the last run timestamp plus initiating email after the job completes or stops.

## Abort

A queued or running ASIN Sync job can be aborted manually. ASIN Sync is currently the only manually-invoked sync job (of Summary Sync, ASIN Sync, Fan Expo Sync, GalaxyCon Sync) with an abort control — it was added specifically for ASIN Sync first; the others do not yet support it.

Abort is cooperative: it sets an in-memory signal checked between candidates (including during the per-candidate delay), so the job stops within roughly one ScraperAPI request (bounded by `REQUEST_TIMEOUT`) of the abort request, not instantly. If the worker thread that owns the job is no longer tracked in the current process (e.g. the app restarted after the job was left `running`), abort instead force-stops the row directly so the active-job lock clears.

An aborted job is persisted with status `stopped`, the same status used for a rate-limit pause — distinguish them by the job's `message` field (`"ASIN sync aborted by admin."` vs the rate-limit message).

**Audit log caveat:** an abort is currently recorded in the audit log identically to an unexpected crash or a rate-limit stop — `action="sync_failed"`, `status="failed"` — because both paths share the same `_stop_job` helper. There is no distinct audit action for "an admin deliberately aborted this," so audit log review cannot yet tell a deliberate abort apart from a real failure.

## Client Entry Point

ASIN Sync appears in the Admin page Archive Import section directly below Summary Sync. While a job is queued or running, an Abort button appears next to the ASIN Sync button.

## Server Entry Points

ASIN Sync APIs are served under `/admin/book-asin/sync`, including `POST /admin/book-asin/sync/{job_id}/abort`.

All ASIN Sync APIs require `admin` or `power_user` access. If ScraperAPI is not configured, the APIs return `502 Bad Gateway`. Abort returns `409 Conflict` if the job is not currently queued or running.

When `SCHEDULER_ENABLED=true`, ASIN Sync also runs daily at `ASIN_SYNC_SCHEDULE_TIME` in `SCHEDULER_TIMEZONE`. Scheduled runs use `SCHEDULER_EMAIL` for audit log identity and do not authenticate as a user.

## Failure Handling

Individual ISBN lookup failures do not stop the full job. The failed ISBN is recorded and the job continues to the next candidate.

If ScraperAPI rate-limits a request (`429`), the job stops immediately and the ISBN is recorded with a 24-hour retry window.

If the job stops unexpectedly, the persisted job status is set to `failed` and the Admin page displays the failure state.

Manual ASIN Sync requests return `409 Conflict` when a non-stale ASIN Sync job is already queued or running. Stale active jobs older than `SCHEDULER_TIMEOUT_MINUTES` are marked failed before a new manual or scheduled run starts. Aborting a stuck job is the faster manual alternative to waiting out the staleness timeout.
