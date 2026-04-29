# About Version Workflow

## Rules

About modal displays app name and app version.

Version is loaded from the support version API.

## Client Side Validation

Client side validation occurs when the About menu item is clicked.

On click:
1. Open About modal
2. Call version API
3. Display version when loaded
4. Display `unknown` if version lookup fails

## Server Side Validation

Version API does not require authenticated user access.

Version API returns the current app version.

## Success Workflow

1. User opens account menu
2. User clicks About
3. Client opens About modal
4. Client calls version API
5. API returns app version
6. Client displays app name and version
7. User clicks Close to dismiss modal

## Failure Workflow

1. If version API fails, display version as `unknown`
2. Keep About modal open
