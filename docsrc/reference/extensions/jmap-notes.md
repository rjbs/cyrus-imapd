# https://cyrusimap.org/ns/jmap/notes

**Capability URI**: `https://cyrusimap.org/ns/jmap/notes`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability adds the Note datatype for managing personal notes stored on the
server.  Notes are stored in a dedicated notes folder using an IMAP-based backend.

## Note

### Note/get

### Note/set

### Note/changes

Each Note object has the following properties:

- **id**: `Id` (server-set, immutable)

- **isFlagged**: `Boolean`  Whether the note has been flagged by the user.  Maps
  to the IMAP `\Flagged` flag.

- **title**: `String`  The title of the note.

- **body**: `String`  The content of the note.

- **isHTML**: `Boolean`  Whether the body is HTML (`true`) or plain text (`false`).

- **lastSaved**: `UTCDate` (server-set)  The time the note was last modified on
  the server.
