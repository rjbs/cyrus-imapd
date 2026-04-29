# https://cyrusimap.org/ns/jmap/core

**Capability URI**: `https://cyrusimap.org/ns/jmap/core`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This server capability (not an account capability) does not add any new methods.
It extends the core server capability object with one additional field:

- **maxCreatedIdsInRequest**: `UnsignedInt`  The maximum number of creation IDs
  that a client may reference within a single JMAP request.
