# Canonical ingress and struct process

## Invariant

Online and offline inputs MUST be converted into the same canonical envelope and the same typed internal process structures before PC2 validation. This is a normative invariant, not an optimization.

```text
OFFLINE FILE / LOCAL SENSOR ─┐
                             ├─> TRANSPORT ADAPTER
ONLINE API / REMOTE SOURCE ──┘          |
                                        v
                               CANONICAL ENVELOPE
                                        |
                              SCHEMA + SIZE VALIDATION
                                        |
                              PROVENANCE VALIDATION
                                        |
                               PARSE TO CANONICAL AST
                                        |
                              INFERENCE / LOOP_CYCLE
                                        |
                                   SFTR GATE
                                        |
                              SEPARATE AUTHORIZATION
```

## Canonical envelope

The language-neutral envelope is specified in [canonical-envelope.schema.json](../schemas/canonical-envelope.schema.json). It contains:

- `schema_version`: explicit, versioned schema identifier;
- `event_id`: stable unique event identifier;
- `session_id`: processing session;
- `source`: source identity/type and source-local identifier;
- `transport`: ingress channel metadata, never a trust level;
- `payload`: typed content, not an unvalidated free-form control instruction;
- `provenance`: digest, algorithm, capture metadata and verification state;
- `time`: capture and ingest times with declared clock domains;
- `quality`: completeness, freshness and validation flags;
- `classification`: `SIMULATED`, `LIVE_UNVERIFIED`, `LIVE_VERIFIED` or `UNAVAILABLE`.

The envelope must not contain a caller-controlled `execution_permitted=true` field. Permission is derived by the server-side policy gate after all checks.

## Adapter contract

Each adapter performs only transport work:

1. Receive bytes or a local object.
2. Enforce transport size/rate limits.
3. Preserve original bytes or a lossless source reference for provenance.
4. Parse transport syntax into the canonical envelope.
5. Set transport metadata from the adapter itself, not from user payload claims.
6. Return a typed result: `ADAPTED`, `REJECTED` or `UNKNOWN`.

Adapters MUST NOT:
- set SFTR to PASS;
- grant capabilities;
- infer truth or causality from source type;
- silently repair malformed values;
- replace missing timestamps, hashes, sensor samples or identities with fabricated defaults;
- downgrade validation for offline inputs or upgrade trust for online inputs.

## Canonical process structs

Implementation language structs should mirror the JSON Schema exactly. The parser receives a validated envelope and emits a canonical AST. The inference layer receives that AST and explicit rule-set version. The SFTR aggregator receives named check results and computes tri-state status.

Suggested Rust outline:

```rust
enum ValidationState { Pass, Fail, Unknown }

enum DataClass {
    Simulated,
    LiveUnverified,
    LiveVerified,
    Unavailable,
}

struct CanonicalEnvelope {
    schema_version: String,
    event_id: String,
    session_id: String,
    source: SourceRef,
    transport: TransportMeta,
    payload: Payload,
    provenance: Provenance,
    time: TimeMeta,
    quality: QualityMeta,
    classification: DataClass,
}

struct CheckResult {
    check_id: String,
    state: ValidationState,
    evidence_refs: Vec<String>,
    diagnostics: Vec<String>,
}
```

This is an illustrative contract outline, not compiled or tested Rust code. Production structs should use enums/newtypes and validated constructors rather than accepting arbitrary strings.

## Same-process equivalence

Given equivalent canonical envelopes, offline and online execution must produce equivalent:
- canonical payload and AST;
- parser version and grammar result;
- provenance-validation result;
- inference rule-set and proof object;
- SFTR decision;
- authorization requirements.

Transport metadata may differ and must remain visible in provenance. A transport difference is not itself a reason to change logical validity.

## Reject / UNKNOWN rules

- Malformed schema, invalid required field, invalid digest or explicit invariant violation: reject / FAIL as defined by the relevant layer.
- Missing evidence, unknown key, unsupported schema version, unresolved provenance or unavailable verifier: UNKNOWN and no certification.
- Offline network absence: expected mode, not failure.
- Online service outage: UNKNOWN/unavailable, never a fallback to unverified success.
