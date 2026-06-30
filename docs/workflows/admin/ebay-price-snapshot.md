# eBay Price Snapshot Workflow

eBay Price Snapshot requires role `admin` or `power_user`.

The job captures active eBay Browse API listing prices for catalog books with valid ISBNs. It calculates aggregate pricing in memory and stores one daily aggregate row per book in `book_price_snapshot`.

Only books with checksum-valid ISBN-10 or ISBN-13 values are eligible. ISBN-10 values are converted to ISBN-13 before searching. Books that already have an eBay active-listing snapshot for the current date are skipped so manual reruns do not duplicate daily chart points.

Search strategy:

1. Search Browse API with `gtin=<isbn13>`.
2. If no listings are returned, search Browse API with `q=<isbn13>`.
3. Title/author fallback is intentionally out of scope for the first implementation.

Job counts:

- `total`: eligible books selected for this run.
- `processed`: books attempted.
- `snapshotted`: books with at least one active listing stored.
- `no_results`: books where both ISBN searches returned no listings.
- `failed`: books whose eBay request or persistence failed.
- `skipped`: invalid ISBNs, duplicate ISBNs within candidate selection, already-snapshotted books, or books beyond the configured batch cap.

Config:

- `EBAY_PRICE_SNAPSHOT_ENABLED`: enables scheduled runs when `true`.
- `EBAY_PRICE_SNAPSHOT_SCHEDULE_TIME`: daily schedule time, default `04:00`.
- `EBAY_PRICE_SNAPSHOT_BATCH_SIZE`: optional cap for rollout/testing. Blank or missing means all eligible books.
- `EBAY_PRICE_SNAPSHOT_DELAY_SECONDS`: delay between books, default `1`.

Manual APIs are served under `/admin/ebay-price-snapshot`, including `POST /admin/ebay-price-snapshot/{job_id}/abort`.

When `SCHEDULER_ENABLED=true` and `EBAY_PRICE_SNAPSHOT_ENABLED=true`, the job also runs daily at `EBAY_PRICE_SNAPSHOT_SCHEDULE_TIME` in `SCHEDULER_TIMEZONE`. Scheduled runs use `SCHEDULER_EMAIL` for audit log identity and do not authenticate as a user.
