# https://cyrusimap.org/ns/jmap/sieve

**Capability URI**: `https://cyrusimap.org/ns/jmap/sieve`

Requires `jmap_nonstandard_extensions` to be enabled in the Cyrus configuration.

This capability extends Sieve script management in two ways: it re-exposes the
standard SieveScript methods (get, set, query, validate) under this capability
identifier in addition to `urn:ietf:params:jmap:sieve`, and it adds one new
method and one behavioral extension to SieveScript/set.

## Changes to SieveScript

### SieveScript/set

When this capability is in use, `onSuccessActivateScript: null` is accepted as a
way to deactivate the currently active script without activating another.  The
standard capability only allows providing a script ID to activate.

---

### SieveScript/test

Tests a Sieve script against one or more email messages without modifying any
mailbox state.  Useful for verifying that a script produces the expected filtering
actions before deploying it.

**Arguments:**

- **accountId**: `Id`

- **scriptBlobId**: `Id`  The blob ID of the Sieve script to test.  Supports
  the `#` creation-reference syntax.

- **emailBlobIds**: `Id[]`  An array of blob IDs of email messages to run the
  script against.

- **envelope**: `Object|null`  The SMTP envelope to present to the script.
  Contains:
  - **mailFrom**: `Object`  The envelope sender address object.
  - **rcptTo**: `Object[]`  An array of envelope recipient address objects.

- **variables**: `{String: String}|null`  A map of Sieve variable names to
  initial values, for testing scripts that use the Sieve variables extension.

- **lastVacationResponse**: `UTCDate|null`  Simulates the date of the last
  vacation response sent to a given address, for testing vacation scripts.

**Response:**

- **accountId**: `Id`

- **completed**: `{Id: Object[]}`  A map from email blob ID to the array of
  actions the script took for that message (e.g., fileinto, redirect, discard).

- **notCompleted**: `{Id: SetError}`  A map from email blob ID to an error
  object for messages the script could not be tested against.
