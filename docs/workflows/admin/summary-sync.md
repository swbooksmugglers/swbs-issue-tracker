# Summary Sync Workflow

Summary Sync requires role `admin` or `power_user`.

Summary Sync starts a persisted background job. It scans local books that already have a summary and copies that summary to matching local books that do not have one.

Matching is intentionally local and conservative:
1. Source books come from the local `book` table where `summary` is present.
2. Target books must have the same normalized title as the source book.
3. Target books must share at least one contributor with role `author`.
4. The source book itself is ignored.
5. Target books with an existing summary are skipped and not overwritten.

Summary Sync records source-target attempts so later runs can skip pairs already synced from the same source summary version. If the source summary changes later, that source-target pair may be checked again.

The job chooses one eligible source for each unique target book that is missing a summary. The checked and total counts represent unique target books selected for sync, not every possible source-target pair. Duplicate source matches for the same target do not inflate the progress count.

The job records checked, updated, failed, and skipped counts for the Admin progress display. The Admin progress indicator persists the latest job and shows the last run timestamp plus initiating email after the job completes or stops.

## Client Entry Point

Summary Sync appears in the Admin page Archive Import section below Summary Import and above ISBN Import.

## Server Entry Points

Summary Sync APIs are served under `/admin/book-summary/sync`.

All Summary Sync APIs require `admin` or `power_user` access.

## Failure Handling

Individual target update failures do not stop the full job. The failed pair is recorded and the job continues to the next candidate.

If the job stops unexpectedly, the persisted job status is set to `failed` and the Admin page displays the failure state.
