# Account Menu Workflow

## Rules

Account menu requires signed-in user context.

Admin menu item is visible only when role is `admin` or `power_user`.

Settings, Support, About, and Sign out are visible to signed-in users.

## Client Side Validation

Client side validation occurs when Header renders and when the account menu opens.

On render:
1. Decode token role
2. Include Admin menu item only for `admin` and `power_user`
3. Include standard menu items for all signed-in users

## Success Workflow

1. User clicks greeting button
2. Client opens account menu
3. User selects a route or modal action
4. Client closes account menu
5. Client performs selected action

Menu actions are:
1. Admin: navigate to `/admin` (admin and power_user only; shown at top with divider below)
2. Settings: navigate to `/settings`
3. Support: navigate to `/support`
4. Submit a Bug: open Bug Report modal
5. About: open About modal
6. Divider
7. Sign out: call signout workflow

## Failure Workflow

If selected route requires authentication and no token exists, target page redirects to Sign In page.
