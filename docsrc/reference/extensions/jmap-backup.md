# https://cyrusimap.org/ns/jmap/backup

**Capability URI**: `https://cyrusimap.org/ns/jmap/backup`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds the Backup datatype, which provides methods for recovering
deleted or modified data within a configurable lookback window.  The restore
operations work by replaying or reversing changes recorded in the server's
replication log.  Locks are released in batches so that the account remains
usable during a restore.

## Backup

### Backup/restoreContacts

Restores contact data to a prior state.

**Arguments:**

- **accountId**: `Id`

- **undoPeriod**: `String`  An ISO 8601 duration (e.g., `PT2H`, `P1D`) specifying
  how far back to restore.  Changes made within this window are undone.

- **performDryRun**: `Boolean` (default: `false`)  When true, the server
  calculates what would be restored but makes no changes.

- **verboseLogging**: `Boolean` (default: `false`)  When true, the server records
  additional diagnostic information.

- **undoAll**: `Boolean` (default: `false`)  When true, all changes within the
  undo period are reversed, not just the most recent change to each object.

**Response:**

- **accountId**: `Id`

- **numCreatesUndone**: `UnsignedInt`  The number of newly created objects that
  were deleted as part of the restore.

- **numUpdatesUndone**: `UnsignedInt`  The number of updated objects that were
  reverted to a prior version.

- **numDestroysUndone**: `UnsignedInt`  The number of previously deleted objects
  that were recreated.

- **performDryRun**: `Boolean`  Echoed back when true.

---

### Backup/restoreCalendars

Restores calendar data.  Arguments and response are identical to
Backup/restoreContacts.

---

### Backup/restoreNotes

Restores note data.  Arguments and response are identical to
Backup/restoreContacts.

---

### Backup/restoreMail

Restores email and mailbox data.  All arguments from Backup/restoreContacts
apply, plus:

**Additional arguments:**

- **restoreDrafts**: `Boolean` (default: `true`)  When true, draft messages
  deleted within the undo period are restored.

- **restoreNonDrafts**: `Boolean` (default: `true`)  When true, non-draft
  messages deleted within the undo period are restored.

**Response:**

- **accountId**: `Id`

- **numDraftsRestored**: `UnsignedInt`  The number of draft messages restored.

- **numNonDraftsRestored**: `UnsignedInt`  The number of non-draft messages
  restored.

- **performDryRun**: `Boolean`  Echoed back when true.
