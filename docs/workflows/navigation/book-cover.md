# Book Cover Workflow

## Rules

Book cover uses OpenLibrary cover image by ISBN first.

Google Books cover fallback is disabled by default. When `GOOGLE_BOOKS_COVER_FALLBACK_ENABLED=true`, if OpenLibrary does not have cover art, query Google Books by ISBN and use the best available `volumeInfo.imageLinks` URL.

If Google Books has no ISBN match with cover art, query OpenLibrary search by title and author when available, then use the first result with `cover_i`.

When Google Books cover fallback is enabled and OpenLibrary search has no cover art, query Google Books by title and author when available.

If none of the cover sources have cover art, display a placeholder.

Cover image must include accessible alt text.

## Client Side Validation

Client side behavior occurs when a book result row renders.

On render:
1. Normalize ISBN for cover lookup by removing display punctuation
2. Build OpenLibrary cover image URL using normalized ISBN
3. If OpenLibrary fails, query Google Books volumes by normalized ISBN
4. If Google Books ISBN lookup has no image, query OpenLibrary search by title and author
5. If OpenLibrary search returns `cover_i`, build an OpenLibrary cover URL by cover ID
6. If OpenLibrary search has no cover, query Google Books by `intitle` and `inauthor` when author is available
7. Use the largest available Google Books image link when present
8. Normalize Google Books image URLs to HTTPS
9. Set alt text using book title
10. Display cover image or placeholder

## Success Workflow

1. Book row renders with ISBN and title
2. Client requests OpenLibrary cover image
3. Image loads successfully
4. Client displays book cover

## Secondary Success Workflow

1. OpenLibrary image request fails
2. Client requests Google Books volume metadata by ISBN
3. Google Books returns an image link
4. Client displays Google Books cover image

## Tertiary Success Workflow

1. OpenLibrary image request fails
2. Google Books ISBN lookup returns no image link
3. Client requests OpenLibrary search by title and author
4. OpenLibrary returns a result with `cover_i`
5. Client displays OpenLibrary cover by cover ID

## Quaternary Success Workflow

1. OpenLibrary image request fails
2. Google Books ISBN lookup returns no image link
3. OpenLibrary search returns no cover ID
4. Client requests Google Books volume metadata by title and author
5. Google Books returns an image link
6. Client displays Google Books cover image

## Failure Workflow

1. OpenLibrary image request fails
2. Google Books ISBN lookup fails or returns no image link
3. OpenLibrary title and author search fails or returns no cover ID
4. Google Books title and author lookup fails or returns no image link
5. Client displays placeholder
6. Placeholder has accessible label indicating no cover is available

## Server Side Validation

No app server side validation is needed for book cover loading because image loading is client side.
