# Admin Audit Log Workflow

## Purpose

The Audit Log gives admins a read-only view of authenticated create, update, delete, and blocked-delete activity.

## Access

Only users with role `admin` can view audit logs.

Power users and regular users must not see audit log data or controls.

## Client Flow

1. Admin opens the Admin page.
2. Admin sees the Audit Log section.
3. Client loads `/api/admin/audit-log`.
4. Admin can filter by search text, action, status, and entity type.
5. Admin can sort by date, user, action, entity, and status. Sorting is server-side and reloads from the API.
6. Admin can open an entry details dialog.
6. Details display request metadata, changed fields, before values, and after values.

## Server Flow

Audit log APIs are served under `/admin/audit-log`.

The list API accepts optional `sort_by` and `sort_order` query parameters. Allowed `sort_by` values are `occurred_at`, `user_email`, `action`, `entity_type`, and `status`. The default sort is `occurred_at DESC`. `sort_order` accepts `asc` or `desc`.

Read APIs must use `require_admin`.

Mutation workflows write audit entries from the backend after successful database writes.

Blocked delete workflows write audit entries with `status = 'blocked'`.

New audit entries should use human-readable entity type values such as `book`, `book summary`, `sub-series`, `author`, `publisher`, `exclusive`, `era`, `series`, `role`, `user`, and `profile`.

Historical audit rows are not rewritten when entity wording changes.

## Fields

Audit entries include:

1. User ID and user email snapshot
2. Action
3. Entity type and entity ID
4. Timestamp
5. Before values
6. After values
7. Changed fields
8. Request path and method
9. Status
10. Message

## Security

Audit snapshots must be sanitized before storage.

Never store passwords, password hashes, reset tokens, activation tokens, JWTs, SMTP credentials, database credentials, or API keys.
