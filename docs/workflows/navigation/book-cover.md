# Book Cover Workflow

## Rules

Book cover uses OpenLibrary cover image by ISBN.

If cover image fails to load, display a placeholder.

Cover image must include accessible alt text.

## Client Side Validation

Client side behavior occurs when a book result row renders.

On render:
1. Build OpenLibrary cover image URL using ISBN
2. Set alt text using book title
3. Display cover image

## Success Workflow

1. Book row renders with ISBN and title
2. Client requests OpenLibrary cover image
3. Image loads successfully
4. Client displays book cover

## Failure Workflow

1. OpenLibrary image request fails
2. Client marks cover as failed
3. Client displays placeholder
4. Placeholder has accessible label indicating no cover is available

## Server Side Validation

No app server side validation is needed for book cover loading because image loading is client side.
