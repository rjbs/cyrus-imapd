# https://cyrusimap.org/ns/jmap/admin

**Capability URI**: `https://cyrusimap.org/ns/jmap/admin`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability is only advertised for accounts belonging to server administrators
(i.e., users with the `httpd_userisadmin` flag set).  It provides maintenance
methods for bulk calendar data operations.

## Admin

### Admin/rewriteCalendarEventPrivacy

Rewrites the `privacy` property on CalendarEvent objects to conform to the current
server policy.  This is intended for use after a policy change, to bring existing
events into compliance.

**Arguments:**

- **accountId**: `Id`

- **userIds**: `String[]|null`  The list of user account IDs to process.  If
  omitted or null, all users on the server are processed.

**Response:**

- **accountId**: `Id`

- **rewritten**: `{String: Boolean}`  A map from user ID to `true` for each
  user whose events were successfully processed.

- **notRewritten**: `{String: SetError}` (omitted if empty)  A map from user ID
  to an error object for each user whose events could not be processed.

---

### Admin/migrateCalendarDefaultAlarms

Migrates calendar default alarms from the CalDAV-level representation to the
JSCalendar `CalendarEvent` representation used by current Cyrus releases.  This
is intended to be run once after upgrading from an older server version.

**Arguments:**

- **accountId**: `Id`

- **userIds**: `String[]|null`  The list of user account IDs to process.  If
  omitted or null, all users on the server are processed.

- **keepCaldavAlarms**: `Boolean` (default: `false`)  When false, the old
  CalDAV alarm representation is removed after migration.  When true, the old
  representation is left in place.

**Response:**

- **accountId**: `Id`

- **migrated**: `{String: Boolean}`  A map from user ID to `true` for each
  user whose alarms were successfully migrated.

- **notMigrated**: `{String: SetError}` (omitted if empty)  A map from user ID
  to an error object for each user that could not be migrated.
