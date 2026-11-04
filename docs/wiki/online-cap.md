# Online CAP

## Definition

The online profile may use explicitly approved remote sources, but online transport is not a trust level. Remote content is untrusted input until it passes the same canonical validation pipeline as offline content.

## Adapter responsibilities

- Authenticate remote service connections using approved credentials and modern transport security.
- Enforce endpoint allowlists, request timeouts, response-size limits and rate limits.
- Validate remote response media type and schema.
- Preserve source identifiers, response metadata and original payload bytes or an immutable source reference.
- Convert response into the canonical envelope.
- Mark source authenticity only when independently verified (for example, a trusted signature or authenticated source channel with documented guarantees).
- Record fetch time and source timestamp separately.
- Return `UNKNOWN` when remote provenance cannot be verified.

## Prohibited shortcuts

- No direct remote API result to inference or defense synthesis.
- No “online verified” flag set by payload content.
- No API response field may set `execution_permitted`.
- No remote model may author its own verification certificate.
- No network timeout, 5xx, malformed response or missing signature may be treated as empty-success.
- No automatic fallback from a trusted endpoint to an untrusted mirror.
- No cloud call from the offline profile.

## Online/offline equivalence

The online adapter and offline adapter must produce the same canonical envelope type. Transport-specific metadata is retained in `transport`; it does not alter the payload schema or SFTR rules.

For a paired fixture test:
1. Prepare a canonical payload.
2. Deliver it through a local offline fixture adapter.
3. Deliver the equivalent payload through the online test adapter.
4. Compare canonical payload, AST, provenance result, inference result and SFTR result.
5. Differences are allowed only in explicitly transport-specific metadata and timestamps.
6. If source authenticity differs, the provenance result must reflect that difference; it must not be hidden by normalization.

## Security boundary

Online adapter credentials belong to the adapter runtime, not the canonical payload. Secrets must never be serialized into canonical envelopes, logs, observer events or fixtures. Use least privilege and rotateable credentials. The online adapter cannot write directly to proof ledgers or authorization stores.
