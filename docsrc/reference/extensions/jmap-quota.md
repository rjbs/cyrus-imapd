# https://cyrusimap.org/ns/jmap/quota

**Capability URI**: `https://cyrusimap.org/ns/jmap/quota`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability provides a legacy Quota/get method with a simpler response format
than the standard `urn:ietf:params:jmap:quota` version.  It predates the IETF
standardization of JMAP quota support.

New deployments should prefer `urn:ietf:params:jmap:quota`.

## Quota

### Quota/get

Returns a list of Quota objects.  Typically returns a single object for the mail
quota.

Each Quota object has the following properties:

- **id**: `String` (server-set, immutable)  A stable identifier for this quota
  resource.

- **used**: `UnsignedInt` (server-set)  The number of bytes currently in use.

- **total**: `UnsignedInt` (server-set)  The total quota limit in bytes.
