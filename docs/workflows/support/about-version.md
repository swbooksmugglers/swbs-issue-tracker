# About Version Workflow

## Rules

About modal displays app name, summary, app version, and development team credit.

Version is loaded from the support version API.

## Client Side Validation

Client side validation occurs when the About menu item is clicked.

On click:
1. Open About modal
2. Call version API
3. Display app summary and development team credit
4. Display version when loaded
5. Display `unknown` if version lookup fails

## Server Side Validation

Version API does not require authenticated user access.

Version API returns the current app version.

## Success Workflow

1. User opens account menu
2. User clicks About
3. Client opens About modal
4. Client calls version API
5. API returns app version
6. Client displays app name, summary, version, and development team credit
7. User clicks Close to dismiss modal

## Failure Workflow

1. If version API fails, display version as `unknown`
2. Keep About modal open
