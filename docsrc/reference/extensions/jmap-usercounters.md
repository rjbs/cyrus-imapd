# https://cyrusimap.org/ns/jmap/usercounters

**Capability URI**: `https://cyrusimap.org/ns/jmap/usercounters`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds the UserCounters datatype, which exposes the server's internal
modification sequence counters for all data types in a single request.  These
counters can be used for efficient sync bootstrapping or monitoring.

## UserCounters

### UserCounters/get

Returns a single UserCounters object for the account.

Each UserCounters object has the following properties.  All properties are
server-set and immutable (they reflect current server state; they cannot be
modified by the client).

- **id**: `Id` (server-set, immutable)

- **highestModSeq**: `UnsignedInt`  The highest modseq across all data types.

- **mailModSeq**: `UnsignedInt`
- **calendarModSeq**: `UnsignedInt`
- **contactsModSeq**: `UnsignedInt`
- **notesModSeq**: `UnsignedInt`
- **submissionModSeq**: `UnsignedInt`
- **sieveScriptModSeq**: `UnsignedInt`

- **mailDeletedModSeq**: `UnsignedInt`  The highest modseq of any deleted mail
  object.
- **calendarDeletedModSeq**: `UnsignedInt`
- **contactsDeletedModSeq**: `UnsignedInt`
- **notesDeletedModSeq**: `UnsignedInt`
- **submissionDeletedModSeq**: `UnsignedInt`
- **sieveScriptDeletedModSeq**: `UnsignedInt`

- **mailFoldersModSeq**: `UnsignedInt`  The highest modseq of any mailbox/folder
  change.
- **calendarFoldersModSeq**: `UnsignedInt`
- **contactsFoldersModSeq**: `UnsignedInt`
- **notesFoldersModSeq**: `UnsignedInt`
- **submissionFoldersModSeq**: `UnsignedInt`
- **sieveScriptFoldersModSeq**: `UnsignedInt`

- **mailFoldersDeletedModSeq**: `UnsignedInt`
- **calendarFoldersDeletedModSeq**: `UnsignedInt`
- **contactsFoldersDeletedModSeq**: `UnsignedInt`
- **notesFoldersDeletedModSeq**: `UnsignedInt`
- **submissionFoldersDeletedModSeq**: `UnsignedInt`
- **sieveScriptFoldersDeletedModSeq**: `UnsignedInt`

- **quotaModSeq**: `UnsignedInt`
- **raclModSeq**: `UnsignedInt`  The highest modseq of any rights/ACL change.
- **uidValidity**: `UnsignedInt`  The current UIDVALIDITY for the account's INBOX.
