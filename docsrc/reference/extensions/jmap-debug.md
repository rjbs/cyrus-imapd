# https://cyrusimap.org/ns/jmap/debug

**Capability URI**: `https://cyrusimap.org/ns/jmap/debug`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds diagnostic properties and fields to several datatypes and
methods.  The information exposed is intended for development and troubleshooting
and may change without notice.

## Changes to Calendar

### New Properties

- **x-href**: `String` (server-set)  The CalDAV URL of the calendar collection
  on the server.

## Changes to CalendarEvent

### New Properties

- **debugBlobId**: `Id|null` (server-set)  The blob ID of a debug representation
  of the calendar event.  May be null if no debug blob is available.

## Changes to Email

### Email/query

When this capability is in use, the response includes one additional field:

- **languageStats**: `Object`  Statistics from the search engine's language
  detection pass over the matched messages.  The structure is engine-dependent.

### Email/set

When this capability is in use, the response includes one additional field:

- **debug**: `Object`  Diagnostic information about the bulk-update operation,
  including internal state details from the update planner.

### Email/copy

Same as Email/set: the response includes a **debug** field with bulk-update
diagnostic details.

## Changes to CalendarEvent

### CalendarEvent/get

When this capability is in use, the response for each event may include:

- **isFastPath**: `Boolean`  Whether the event was retrieved via the fast
  (index-only) path or the slower full-parse path.
