# Admin Analytics Workflow

## Purpose

The Analytics page gives admins product activity charts derived from the audit log: activity over time, action breakdown, a repeat-login proxy, and a top-users leaderboard.

## Access

Only users with role `admin` can view the Analytics page.

Power users and regular users must not see analytics data or controls.

## Client Flow

1. Admin opens the header account menu and selects Analytics (separate from the Admin page entry).
2. Client redirects to Sign In if no token is stored, or to Home if the role is not `admin`.
3. Client loads `/api/admin/analytics/activity`, `/api/admin/analytics/actions`, `/api/admin/analytics/users`, and `/api/admin/analytics/active-days` in parallel.
4. Admin can filter all four charts by date range (`date_from`, `date_to`).
5. Admin can filter all four charts by a multi-select user picker with an include/exclude toggle (defaults to include-only). Toggling switches which query parameter is sent — `include_user_ids` or `exclude_user_ids` — never both at once.
6. Top Users masks email addresses by default (`••••••••`), matching the Audit Log section's visibility toggle pattern. Admin can click the eye icon to reveal them.

## Server Flow

Analytics APIs are served under `/admin/analytics`.

| Endpoint | Purpose |
|---|---|
| `GET /admin/analytics/activity` | Audit log row counts grouped by day or week (`interval` param) |
| `GET /admin/analytics/actions` | Audit log row counts grouped by `action` |
| `GET /admin/analytics/users` | Top users by audit log row count (`limit` param) |
| `GET /admin/analytics/active-days` | Users bucketed by distinct active days (`1`, `2-9`, `10-24`, `25-49`, `50-74`, `75-99`, `100+`) |

All four endpoints accept optional `date_from`, `date_to`, `include_user_ids`, and `exclude_user_ids` query parameters. `include_user_ids` and `exclude_user_ids` are repeatable (`?exclude_user_ids=1&exclude_user_ids=2`). If both are supplied, both filters apply (callers should send only one).

Read APIs must use `require_admin`.

Aggregation queries live on `AuditLogService` and read the same `audit_log` table as the Audit Log workflow — no separate analytics table exists.

## Repeat Logins Caveat

Login events are not separately tracked in `audit_log` or on the `users` table. The "Repeat Logins" chart approximates login frequency using the count of distinct calendar days each user appears in the audit log. This undercounts users who are active but make no mutating requests, and is not a literal login count.

## Security

Analytics responses aggregate counts only; they do not return `before_values`, `after_values`, or `message` from `audit_log`.
