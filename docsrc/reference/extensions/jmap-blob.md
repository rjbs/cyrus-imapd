# https://cyrusimap.org/ns/jmap/blob

**Capability URI**: `https://cyrusimap.org/ns/jmap/blob`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability re-exposes the standard Blob methods (get, lookup, upload) under
this capability identifier in addition to `urn:ietf:params:jmap:blob`, and adds
extensions to Blob/lookup and Blob/upload.

The server capability object for this extension contains:

- **maxSizeBlobSet**: `UnsignedInt`  The maximum size in bytes of a single blob
  that may be created via Blob/upload with `catenate`.

- **maxCatenateItems**: `UnsignedInt`  The maximum number of items permitted in a
  single `catenate` array.

- **supportedTypeNames**: `String[]`  The datatype names recognized by the `types`
  argument to Blob/lookup.

- **supportedDigestAlgorithms**: `String[]`  The digest algorithms available for
  blob integrity verification.

## Changes to Blob

### Blob/lookup

When this capability is in use, one additional argument is accepted as an alias:

- **types**: `String[]`  An alias for the standard `typeNames` argument.  The
  two argument names are equivalent; `types` is the older name.

### Blob/upload

When this capability is in use, one additional argument is accepted:

- **catenate**: `CatenateItem[]`  An ordered list of data sources to concatenate
  into a single new blob.  Each item in the array is an object containing exactly
  one source field and optional slice parameters:

  - **blobId**: `Id`  Use an existing blob (or a slice of one) as the data source.
    Supports the `#` creation-reference syntax.

  - **offset**: `UnsignedInt` (default: `0`)  When using `blobId`, the byte
    offset within the source blob at which to begin reading.

  - **length**: `UnsignedInt` (default: remainder of blob)  When using `blobId`,
    the maximum number of bytes to read from the source blob.

  - **data:asText**: `String`  Use a literal UTF-8 string as the data source.

  - **data:asBase64**: `String`  Use a base64-encoded string as the data source.
    The decoded bytes are used, not the base64 representation itself.

  Each item must contain exactly one of `blobId`, `data:asText`, or
  `data:asBase64`.  The `offset` and `length` fields are only valid alongside
  `blobId`.
