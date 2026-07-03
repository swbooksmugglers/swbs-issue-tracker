# eBay Price Snapshot Workflow

eBay Price Snapshot requires role `admin` or `power_user`.

The job captures active eBay Browse API listing prices for catalog books with valid ISBNs. It calculates aggregate pricing in memory and stores one daily aggregate row per book in `book_price_snapshot`.

Only books with checksum-valid ISBN-10 or ISBN-13 values are eligible. ISBN-10 values are converted to ISBN-13 before searching. Books that already have an eBay active-listing snapshot for the current date are skipped so manual reruns do not duplicate daily chart points.

## Price display (Book Price Modal)

The price index endpoint (`GET /api/books/price-snapshots`) returns a **rolling 7-day window** aggregate per book: `observed_on BETWEEN CURRENT_DATE - 7 AND CURRENT_DATE`. Stats are computed across all snapshots in the window:

- **Low**: `MIN(low_price)` across the window.
- **High**: `MAX(high_price)` across the window.
- **Avg**: weighted mean — `SUM(average_price * priced_listing_count) / SUM(priced_listing_count)`.
- **Median**: weighted mean of daily medians — `SUM(median_price * priced_listing_count) / SUM(priced_listing_count)`.

If fewer than 7 days of snapshots exist for a book, the window is calculated from whatever data is available. The response includes `window_start`, `window_end`, `days_with_data`, `priced_listing_count`, and `cover_key` (joined from `book`).

## Price history chart

`GET /api/book/{book_id}/price-history` returns daily rows for the last 90 days, ordered by `observed_on ASC`. Each row contains `observed_on`, `low_price`, `average_price`, `median_price`, `high_price`, and `currency`. The modal renders these as a line chart (Low, Avg, Median, High) using `@mui/x-charts`.

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
