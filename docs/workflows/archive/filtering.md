# Archive Filtering Workflow

## Rules

Archive filtering is client side.

Each exclusive section has its own filter value.

Filter matches title or author.

Exclusive sections can be collapsed and expanded.

## Client Side Validation

Client side validation occurs when a user types in an exclusive filter or clicks an exclusive section heading.

On filter change:
1. Store filter text for that exclusive
2. Convert filter text to lowercase for matching
3. Filter books for that exclusive by title or author

On collapse toggle:
1. Toggle collapsed state for the selected exclusive
2. Preserve filter value for that exclusive

## Success Workflow

1. User opens Archive page
2. User expands or collapses exclusive sections
3. User enters filter text for an exclusive
4. Client filters only that exclusive's books
5. Matching books remain visible
6. Non-matching books are hidden

## Empty Filter Workflow

1. If filter is empty, show all books for that exclusive
2. If filter excludes all books, display no-books-match-filter message in that exclusive table

## Server Side Validation

No server side validation is needed for archive filtering because filtering is client side.
