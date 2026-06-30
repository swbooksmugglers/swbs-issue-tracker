# Cover Art Management Workflow

The Cover Art Management section is visible to `admin` or `power_user` in the Admin UI, but every server endpoint it calls requires `admin`. A `power_user` who opens this section will see the controls but get `403 Forbidden` from every action.

Cover Art Management has three independent sub-features: Missing Covers, Cover Clone, and Cover Upload.

## Missing Covers (Batch Download)

Lists books whose `cover_key` has no matching object in the `swbs-cover-art` Spaces bucket, then starts a persisted background job that downloads and uploads covers for them.

Candidate selection only includes books whose `cover_key` equals their normalized ISBN (variant cover keys — e.g. manually cloned or uploaded covers with a non-ISBN key — are not auto-downloaded).

For each candidate, the job tries OpenLibrary first, then falls back to Google Books cover art if `GOOGLE_BOOKS_COVER_FALLBACK_ENABLED=true`. If neither source has a cover, the candidate is recorded `not_found` and the job continues.

If OpenLibrary rate-limits a request (`429`), the job stops immediately and is marked with a `retry_after` timestamp computed from the `Retry-After` header (or 24 hours if absent).

The job records `downloaded`, `skipped` (cover already exists), `not_found`, and `failed` counts. Requests are spaced by `COVER_DOWNLOAD_REQUEST_DELAY_SECONDS` (default 0.5s).

This same single-cover download logic (`download_single`) also runs inline when an admin creates a new book without an explicit cover image — see `book-management.md`.

## Cover Clone

Copies an existing cover image from one book's cover key to one or more other books' cover keys, without re-uploading image bytes. Used when two editions share the same physical cover art.

1. Admin selects a source book (search by title/author, or browse all titles by a given author) and confirms it already has a cover.
2. Admin selects one or more target books.
3. Client calls the clone API with the source cover key and target cover keys.

By default, a target whose cover key already has an image is skipped (not overwritten) and reported with status `skipped`. Admin can force specific target keys to overwrite via `force_cover_keys`.

If the source cover does not exist in Spaces, the request fails with `404 Not Found` and nothing is cloned.

## Cover Upload

Uploads a single image file and writes it (converted to JPEG) to one or more target cover keys directly — used when no existing cover anywhere in the catalog can be cloned.

Invalid image data (fails to decode) returns `422 Unprocessable Entity` before any writes happen. Each target cover key is written independently; a write failure for one key does not block the others, and per-key results report `uploaded` or `error`.

## Client Entry Point

Cover Art Management appears in the Admin page Data Management section, alongside Archive Import and Archive Management.

## Server Entry Points

Cover Art Management APIs are served under `/admin/book-cover`:
- `GET /admin/book-cover/missing` — list books missing cover art.
- `POST /admin/book-cover/batch` — start the batch download job.
- `GET /admin/book-cover/batch/current`, `GET /admin/book-cover/batch/{job_id}` — job progress.
- `GET /admin/book-cover/clone/titles` — list a given author's book titles for clone source selection.
- `GET /admin/book-cover/clone/search` — search book editions to use as clone source or targets.
- `POST /admin/book-cover/clone` — clone a cover to one or more targets.
- `POST /admin/book-cover/upload` — upload an image and write it to one or more targets.

All of the above require `admin`, regardless of the section's `power_user`-visible UI gating.

## Failure Handling

If Spaces is not configured (`spaces_client` unavailable), all Cover Art Management endpoints return `502 Bad Gateway`.

If the batch download job stops unexpectedly, the persisted job status is set to `failed` and the Admin page displays the failure state.

Manual batch download requests return the existing job (not a new one) when a job is already queued or running, rather than rejecting the request.
