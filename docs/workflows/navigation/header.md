# Header Workflow

## Rules

Header requires a valid token prop.

Header displays app name.

Header displays global search.

Header displays account menu button.

Greeting uses display name when available and first name from JWT otherwise.

## Client Side Validation

Client side validation occurs when Header renders.

On render:
1. Decode token payload
2. Read first name and role from token
3. Read display name from theme preference context
4. Display greeting using display name when set
5. Fall back to first name when display name is not set

## Navigation Workflow

1. User clicks app name
2. Client navigates to Home page with current token state

## Search Workflow

Header search is documented in `docs/context/workflows/search/header-search.md`.

## Failure Workflow

If token cannot be decoded, authenticated route behavior should clear stored token and redirect to Sign In page through API/auth handling.
