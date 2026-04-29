# Exclusives Workflow

## Rules

Archive displays exclusive books that have a retailer assigned.

Books must be grouped by retailer.

Retailer groups must be sorted by retailer name.

Books returned by the API must be ordered by retailer name and title.

## Client Side Validation

Client side validation occurs when Archive page loads.

On client side validation:
1. If token exists, call exclusives API
2. Show loading state while API request is in progress
3. Clear collapsed state when fresh results are loaded

## Server Side Validation

Server side validation must occur at the API level.

The exclusives API must validate:
1. Caller is authenticated
2. Book has a non-null retailer ID

## Success Workflow

1. Archive page loads
2. Client calls exclusives API
3. API returns books with retailer data
4. Client groups books by retailer
5. Client sorts retailer group names
6. Client displays each retailer as a collapsible section
7. Client displays books in a table under each retailer

Displayed fields are:
1. Cover
2. Title
3. ISBN
4. Author
5. Publisher
6. Publication date
7. MSRP
8. Pre-signed status
9. Notes

## Empty State Workflow

1. If API returns no exclusive books, display no-exclusives message

## Failure Workflow

1. If API request fails, display failed-to-load exclusives message
2. If token is invalid, clear stored token and redirect to Sign In page
