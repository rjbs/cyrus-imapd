# https://cyrusimap.org/ns/jmap/performance

**Capability URI**: `https://cyrusimap.org/ns/jmap/performance`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability does not add any new methods or properties.  It enables a single
additional argument on Email/query that controls a server-side query optimization.

## Changes to Email

### Email/query

When this capability is in use, one additional argument is accepted:

- **disableGuidSearch**: `Boolean` (default: `false`)  When true, suppresses the
  GUID-based search index and forces a full sequential scan.  Useful for
  troubleshooting or benchmarking but significantly slower in production.
