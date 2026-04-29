# https://cyrusimap.org/ns/jmap/mail

**Capability URI**: `https://cyrusimap.org/ns/jmap/mail`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds properties to the Mailbox, Email, EmailSubmission, and
Identity datatypes, adds new methods to the Email datatype, and extends several
method arguments and responses.

## Changes to Mailbox

### New Properties

Properties marked `EXTERNAL` are passed through to and from an external data store;
Cyrus does not compute or enforce their values itself.

- **isCollapsed**: `Boolean` (external)  Whether the mailbox should be rendered in
  a collapsed state in the client UI.

- **hidden**: `Boolean` (external)  Whether the mailbox should be hidden from the
  default mailbox list.

- **sort**: `UnsignedInt` (external)  A client-defined sort order for displaying
  this mailbox relative to its siblings.

- **identityRef**: `Id|null` (external)  The ID of the Identity that should be
  used by default when composing messages in this mailbox.

- **autoLearn**: `Boolean` (external)  Whether spam-filter training should be
  applied automatically to messages delivered to this mailbox.

- **learnAsSpam**: `Boolean` (external)  When `autoLearn` is true, whether
  messages delivered here should be learned as spam (`true`) or ham (`false`).

- **autoPurge**: `Boolean` (external)  Whether messages in this mailbox should
  be automatically purged after a set period.

- **purgeOlderThanDays**: `UnsignedInt` (external)  When `autoPurge` is true,
  the age in days beyond which messages are purged.

- **onlyPurgeDeleted**: `Boolean` (external)  When `autoPurge` is true, whether
  only messages with the `\Deleted` flag should be purged (rather than all
  messages older than `purgeOlderThanDays`).

- **suppressDuplicates**: `Boolean` (external)  Whether duplicate messages
  (identified by `Message-ID`) should be silently discarded on delivery.

- **shareWith**: `{Id: Object}|null`  A map of account IDs to rights objects
  describing which other accounts have access to this mailbox.

- **isSeenShared**: `Boolean`  Whether the `\Seen` flag state for messages in
  this mailbox is shared across all accounts that have access to it, rather than
  being per-account.

- **storageUsed**: `UnsignedInt` (server-set)  The total size in bytes of all
  messages in this mailbox.

- **color**: `String|null`  A client-defined color (e.g., a CSS color string) for
  rendering this mailbox in the UI.

- **showAsLabel**: `Boolean`  Whether this mailbox should be displayed as a label
  (tag) rather than a folder in the UI.

- **uniqueId**: `String` (server-set, immutable)  A globally unique, stable
  identifier for this mailbox that persists across renames and moves.  This is the
  underlying IMAP mailbox unique ID.

### Changes to Mailbox/set

One additional argument is accepted:

- **onDestroyMoveToMailboxIfNoMailbox**: `Id|null`  When destroying a mailbox, if
  a message would be left with no remaining mailbox membership, move it to the
  mailbox with this ID rather than destroying it.  If null, messages with no
  remaining mailbox are destroyed.

## Changes to Email

### New Properties

The following are top-level Email properties:

- **addedDates**: `{Id: UTCDate}`  A map from mailbox ID to the date when the
  message was added to that mailbox.  Complements `mailboxIds`.

- **removedDates**: `{Id: UTCDate}`  A map from mailbox ID to the date when the
  message was removed from that mailbox.

- **trustedSender**: `String|null` (server-set, immutable)  The domain or address
  that the server has verified as trusted for this message, based on DKIM, SPF,
  or similar mechanisms.  Null if the message has no trusted sender.

- **spamScore**: `Number|null` (server-set, immutable)  The spam score assigned
  to this message by the server's spam filter, parsed from the `X-Spam-Score`
  header.  Null if no score is available.

- **calendarEvents**: `CalendarEvent[]|null` (server-set, immutable)  An array
  of JSCalendar event objects parsed from `text/calendar` or `application/ics`
  parts in this message's body, or null if no calendar parts are present.
  Duplicate events (same GUID delivered as both `text/calendar` and
  `application/ics`) are deduplicated.

- **previousCalendarEvent**: `CalendarEvent|null` (server-set, immutable)
  A JSCalendar event object decoded from the `X-ME-Cal-Previous` header, which
  carries the prior version of a calendar event for change-diffing purposes.
  Null if the header is absent.  Also includes a `uid` field extracted from the
  `X-ME-Cal-UID` header.

- **snoozed**: `Snoozed|null`  The snooze configuration for this message, or null
  if the message is not snoozed.  A snoozed message is hidden from normal views
  until the snooze time is reached.  The object has the following fields:

  - **until**: `UTCDate`  When the snooze expires and the message becomes visible
    again.

  - **setKeywords**: `{String: Boolean}|null`  A map of keyword names to boolean
    values.  When the snooze expires, these keywords are applied to or removed
    from the message.

  - **moveToMailboxId**: `Id|null`  When the snooze expires, the message is moved
    to the mailbox with this ID.  Null means no move.

- **bimiBlobId**: `Id|null` (server-set, immutable)  The blob ID of the BIMI
  (Brand Indicators for Message Identification) indicator image associated with
  this message, encoded as a blob.  Derived from the `BIMI-Indicator` header.
  Null if no BIMI indicator is present.

- **createdModseq**: `UnsignedInt` (server-set, immutable)  The IMAP modseq value
  at the time this message was first appended to any mailbox.  Useful for ordering
  messages by arrival time independent of `receivedAt`.

- **deliveredTo**: `String|null` (server-set, immutable)  The address from the
  `Delivered-To` header, indicating the final delivery recipient.  Null if the
  header is absent.

The following are **body-part** properties, requested via the `bodyProperties`
argument to Email/get rather than the top-level `properties` argument:

- **imageSize**: `UnsignedInt|null`  The size in pixels of this image body part
  (for image/* parts), read from a server-side vendor annotation.  Null if
  no size information is available.

- **isDeleted**: `Boolean`  Whether this body part represents a deleted attachment
  (indicated by a `text/x-me-removed-file` MIME type).

### Changes to Email/query

When this capability is in use, additional arguments are accepted:

- **findMatchingParts**: `Boolean` (default: `false`)  When true, the server
  populates the `partIds` field in the response.

- **findAllInThread**: `Boolean` (default: `false`)  When true, the server
  includes IDs of all messages in a matching thread, not just the matching
  message itself.

Additional sort fields are accepted in the `comparator` array:

- `addedDates` — sort by the date the message was added to the mailbox.  Requires
  a `mailboxId` field on the comparator object.
- `threadSize` — sort by the number of messages in the thread.
- `spamScore` — sort by spam score.
- `snoozedUntil` — sort by snooze-until time.  Requires a `mailboxId` field.

The response includes one additional field when `findMatchingParts` is true:

- **partIds**: `{Id: String[]}|null`  A map from email ID to an array of body
  part IDs that matched the search query within that message.  Null if no matching
  parts were identified.

### Changes to Email/import

When this capability is in use, one additional argument is accepted on each import
object:

- **partIds**: `{Id: String[]}|null`  Associates body part IDs with the imported
  message, used to pre-populate the `partIds` index for subsequent Email/query
  searches.

## Changes to EmailSubmission

### New Properties

- **onSend**: `Object` (immutable)  An object describing additional actions to
  perform when the submission is sent.  Contains:

  - **moveToMailboxId**: `Id|null`  The ID of the mailbox to move the sent
    message into after successful submission.  Null means no move.

- **created**: `UTCDate` (server-set, immutable)  The time at which this
  EmailSubmission object was created on the server.

### Changes to EmailSubmission/query

When this capability is in use, additional filter fields are accepted:

- **createdBefore**: `UTCDate`  Return only submissions created before this time.

- **createdAfter**: `UTCDate`  Return only submissions created at or after this
  time.

Sorting by `created` is also accepted in the `comparator` array.

## Changes to Identity

### New Properties

All Identity extension properties are `EXTERNAL`: they are stored and managed by
an external system and passed through by Cyrus without interpretation.

- **displayName**: `String`  A human-readable name for this identity, for display
  in client UIs (distinct from the `name` property, which is used in From headers).

- **addBccOnSMTP**: `Boolean`  Whether the client should add a Bcc to itself when
  sending via this identity.

- **saveSentToMailboxId**: `Id|null`  The ID of the mailbox where sent messages
  should be saved when using this identity.  Null disables automatic saving.

- **saveOnSMTP**: `Boolean`  Whether the message should be saved to the sent
  folder by the SMTP server rather than by the client.

- **useForAutoReply**: `Boolean`  Whether this identity should be used for
  automatic reply messages (e.g., vacation responses).

- **isAutoConfigured**: `Boolean`  Whether this identity was configured
  automatically by the server (e.g., derived from LDAP or provisioning data).

- **enableExternalSMTP**: `Boolean`  Whether this identity may use an external
  SMTP server (see `smtpServer` and related fields).

- **smtpServer**: `String`  The hostname of the external SMTP server for this
  identity.

- **smtpPort**: `UnsignedInt`  The port number of the external SMTP server.

- **smtpSSL**: `String`  The SSL/TLS mode for the external SMTP connection.
  Typically one of `ssl`, `tls`, or `none`.

- **smtpUser**: `String`  The username for authenticating to the external SMTP
  server.

- **smtpPassword**: `String`  The password for authenticating to the external
  SMTP server.

- **smtpRemoteService**: `String|null`  An opaque identifier for a remote service
  configuration associated with this SMTP identity.

- **popLinkId**: `Id|null`  The ID of a linked POP account, if this identity is
  associated with one.

## New Methods

### Email/lookup

Looks up one or more email messages by a non-ID identifier (e.g., a blob ID or
internal reference) and returns their Email IDs.

### Email/matchMime

Tests a raw MIME message blob against a JMAP email filter and returns whether it
matches, without storing the message.  Useful for pre-filtering or testing
filter rules.
