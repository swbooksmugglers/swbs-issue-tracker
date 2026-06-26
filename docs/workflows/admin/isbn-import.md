# ISBN Import Workflow

## Rules

ISBN Import requires role `admin` or `power_user`.

Import searches ISFDB by ISBN, falls back to Google Books metadata, and finally falls back to Amazon product data through ScraperAPI.

Import performs best-effort Amazon ASIN enrichment whenever ScraperAPI is configured.

Imported data pre-fills the Add Book form.

Import can create missing author, contributor, and publisher records.

Import does not create the book record until the user saves the Add Book form.

## Client Side Validation

Client side validation occurs when the ISBN Import modal is submitted.

On client side validation failure
1. If ISBN is fewer than 3 characters after trimming whitespace, keep Import button disabled
2. This should not trigger a backend call for ISBN import

## Server Side Validation

Server side validation must occur at the API level.

The ISBN import API must validate:
1. Caller has role `admin` or `power_user`
2. ISBN is present
3. At least one configured metadata source returns a usable publication record

The API removes ISBN hyphens and canonicalizes valid ISBN-10 or ISBN-13 values to ISBN-13 before searching.

Metadata lookup order:
1. ISFDB
2. Google Books, when ISFDB returns no usable publication
3. Amazon through ScraperAPI, when ISFDB and Google Books return no usable publication

Google Books summary lookup runs when the selected publication data does not already include a summary.

ScraperAPI Amazon lookup also runs as ASIN enrichment. If ISFDB or Google Books already returned metadata, ScraperAPI ASIN lookup failures do not block the import and the response may include `asin: null`.

If Amazon is needed as the final metadata fallback and ScraperAPI is rate-limited, return a quota-exceeded response.

If all configured metadata sources return no matching publication, return not found.

If author, contributor, or publisher from the selected metadata source does not exist locally, create it.

Author and publisher names are normalized before lookup and storage: accented characters are converted to their ASCII equivalents (e.g. "Córdova" → "Cordova").

## Success Workflow

1. Admin or power user opens Admin page
2. User clicks ISBN Import
3. User enters ISBN
4. Optionally, user clicks the scan barcode action, grants camera access, scans an ISBN barcode, and the client fills the ISBN field without submitting
5. User clicks Search
6. Client trims ISBN and validates length is at least 3 characters
7. Client calls ISBN import API
8. API searches ISFDB by ISBN
9. API falls back to Google Books metadata when ISFDB has no usable publication
10. API enriches summary from Google Books when needed
11. API searches Amazon through ScraperAPI for ASIN enrichment
12. API falls back to Amazon product data when ISFDB and Google Books have no usable publication
13. API parses publication data
14. API upserts authors, contributors, and publisher when needed
15. API resolves matching book type when possible
16. API returns prefill data
17. Client closes ISBN Import modal
18. Client opens Add Book modal with imported fields prefilled
19. Client displays creation notice for any newly created author or publisher
20. User completes any missing required book fields
21. User saves Add Book form

Prefilled fields can include:
1. Title
2. ISBN
3. Cover key (normalized ISBN — hyphens stripped)
4. Publication date
5. MSRP
6. Notes
7. ASIN
8. Summary
9. Author or contributors
10. Publisher
11. Format
12. Continuity (inferred from publication date when not explicitly set)
13. Series
14. Sub-series

## Summary Import Workflow

Summary Import requires role `admin` or `power_user`.

The batch import starts a persisted background job. It scans local books that have no summary, deduplicates by normalized ISBN, skips ISBNs whose latest attempt is still inside its retry window, looks up Google Books by each remaining ISBN, and saves the returned summary when one is found.

Missing Google Books summaries and individual transient lookup failures do not stop the batch. The job records checked, updated, unavailable, failed, and skipped counts for the client progress display.

Summary attempt retry windows:
1. `not_found` retries after 90 days.
2. `failed` retries after 24 hours.
3. `rate_limited` retries after the provider `Retry-After` value or 24 hours.
4. `provider_blocked` requires configuration review before retrying.

The batch stops immediately on Google Books `403` or `429` responses. Backend requests to Google Books are spaced by `SUMMARY_IMPORT_REQUEST_DELAY_SECONDS`.

Summary import jobs store the initiating user ID and email snapshot. The Admin progress indicator persists the latest job and shows the last run timestamp plus initiating email after the job completes or stops.

## Failure Workflow

1. If ISBN is too short, keep Import button disabled
2. If a required metadata fallback is rate-limited, display API error message
3. If no configured source returns a usable record, display API error message
4. If local database update fails, display API error message
5. If network fails, display network error message
6. Keep the ISBN Import modal open

## Amazon Import Endpoint

The backend keeps `GET /api/admin/amazon/import/{isbn}` as a dedicated Amazon/ScraperAPI import endpoint.

The Admin page no longer exposes a separate Amazon Import button. The ISBN Import flow uses the same Amazon lookup behavior internally for final metadata fallback and ASIN enrichment.
