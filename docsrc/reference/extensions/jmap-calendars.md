# https://cyrusimap.org/ns/jmap/calendars

**Capability URI**: `https://cyrusimap.org/ns/jmap/calendars`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds properties to the Calendar and CalendarEvent datatypes, adds
a new method, and alters several behavioral defaults.

The server capability object advertises `isRFC: true` to indicate that the
underlying CalDAV/JSCalendar implementation follows the current RFC drafts.

## Changes to Calendar

### New Properties

The following properties are marked `EXTERNAL`, meaning that Cyrus passes them
through to and from an external data store rather than managing their values
itself.  Cyrus cannot compute them independently.

- **syncedFrom**: `String` (external)  An opaque token or reference indicating
  the external source that this calendar is synchronized from.

- **isEventsPublic**: `Boolean` (external)  Whether events in this calendar
  are publicly visible.

- **isFreeBusyPublic**: `Boolean` (external)  Whether free/busy information for
  this calendar is publicly visible.

- **eventsUrl**: `String` (server-set, external)  A URL at which the events in
  this calendar can be accessed publicly.

- **freeBusyUrl**: `String` (server-set, external)  A URL at which free/busy
  information for this calendar can be accessed publicly.

- **calDavUrl**: `String` (server-set, external)  The CalDAV URL of the calendar
  collection on the server.

The following properties are computed or managed by Cyrus:

- **mailboxUniqueId**: `String` (server-set)  The unique ID of the underlying
  IMAP mailbox that stores this calendar's events.

- **schedulingEnabled**: `Boolean`  Whether CalDAV Auto-Schedule (RFC 6638) is
  active for this calendar.  Defaults to `true`.  When set to `false`, iTIP
  scheduling messages are suppressed for events stored in this calendar.  This
  property maps to the `CY:scheduling-enabled` DAV property and is stored as a
  mailbox annotation.  Implemented in `imap/jmap_calendar.c`.

## Changes to CalendarEvent

### New Properties

- **x-href**: `String`  The CalDAV URL of this event resource on the server.

- **blobId**: `Id|null` (server-set)  The blob ID of the raw iCalendar (`.ics`)
  data for this event.  Can be fetched via Blob/get.  Null if not yet available.

- **cyrusimap.org:iCalProps**: `Array` (server-set)  An array of jCal-format
  property arrays representing iCalendar properties that are not mapped to any
  JSCalendar field.  These are roundtripped transparently on read and write.  The
  format follows RFC 7265 (jCal).  This property is excluded from the default
  property list and must be requested explicitly.

### Behavioral Changes

- **participantIdentities**: CalendarEvent/set accepts a `participantIdentities`
  field (an array of objects with `name`, `type`, and `uri` fields) on the event
  object.  Without this extension, `participantIdentities` is rejected as unknown.

- **Link blobId handling**: When reading event links, if a link has a `blobId`
  the server omits the `href` field from the returned link object.  Without this
  extension, `blobId` is omitted from links instead.

- **isOrigin** in CalendarEvent/set responses: The server includes an `isOrigin`
  field (`Boolean`) in the server-set properties returned for created and updated
  events.  This indicates whether the server considers itself the scheduling
  organizer for the event (i.e., whether this account owns the iTIP state).

## New Method: CalendarEvent/participantReply

Allows a participant to send an iTIP reply to a calendar event invitation without
modifying the stored event directly.
