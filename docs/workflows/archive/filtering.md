# Archive Filtering Workflow

## Rules

Archive filtering is client side.

Each retailer section has its own filter value.

Filter matches title or author.

Retailer sections can be collapsed and expanded.

## Client Side Validation

Client side validation occurs when a user types in a retailer filter or clicks a retailer section heading.

On filter change:
1. Store filter text for that retailer
2. Convert filter text to lowercase for matching
3. Filter books for that retailer by title or author

On collapse toggle:
1. Toggle collapsed state for the selected retailer
2. Preserve filter value for that retailer

## Success Workflow

1. User opens Archive page
2. User expands or collapses retailer sections
3. User enters filter text for a retailer
4. Client filters only that retailer's books
5. Matching books remain visible
6. Non-matching books are hidden

## Empty Filter Workflow

1. If filter is empty, show all books for that retailer
2. If filter excludes all books, display no-books-match-filter message in that retailer table

## Server Side Validation

No server side validation is needed for archive filtering because filtering is client side.
