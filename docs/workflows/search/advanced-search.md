# Filter Workflow

## Rules

The Filter dialog lets users on the Search Results page narrow results by categorical attributes using server-side filtering.

Filter is only available on the Search Results page. It is not accessible from the header or other pages.

## Filter Options

Users can filter by:
1. Publisher
2. Book type
3. Series
4. Sub-series (only enabled when a series is selected; clears when series changes)
5. Era
6. Exclusive
7. Limited edition type
8. Signed (Any / Yes / No)

Filter options are loaded from `GET /api/book/search-filters` when the dialog is first opened.

## Current Workflow

1. User performs a search and lands on Search Results
2. User clicks the **Filter** button next to the results heading
3. Client opens the Filter dialog, pre-populated with any active filters
4. User adjusts filter selections
5. User clicks **Apply** — client re-navigates to `/search` with the same query and new filters in route state
6. Search Results re-runs the search with filter params appended to the API call
7. User clicks **Clear** — all filters are reset and the search is re-run unfiltered
8. User clicks **Cancel** — dialog closes, active filters and results are unchanged

## Server Side

Filter params are passed as optional query params on `GET /api/book/search`:
- `publisher_id`, `book_type_id`, `series_id`, `sub_series_id`, `era_id`, `exclusive_id`, `limited_edition_type_id` (integers)
- `signed` (boolean)

All filter params are optional. Omitted params are not applied.

Filter options are served by `GET /api/book/search-filters` which returns available values for all filter dimensions.
