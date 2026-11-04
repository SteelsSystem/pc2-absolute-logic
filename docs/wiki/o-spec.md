# O-Spec: Observer and Explicit Logic Contract

Status: normative architecture specification; implementation and enforcement are not yet verified.

## 1. Purpose

O is the Observer and observability plane. It records and exposes what system components report. O does not determine truth, validate authorship, mutate application state, grant capabilities, or authorize execution.

O is orthogonal to the Deep Ingress and verification pipeline. It may observe every stage, but it is not a stage that can promote a result.

## 2. Authority boundary

O MAY:
- read explicitly published events, status records, metrics, dependency metadata, and bounded diagnostics;
- correlate events by event ID, session ID, sequence number, component identity, and clock domain;
- report missing, delayed, duplicated, conflicting, stale, or unavailable observations;
- expose the exact source and version of each reported value;
- retain minimized audit records under an explicit retention policy.

O MUST NOT:
- write to canonical input, AST, evidence, proof, authorship, SFTR, or authorization state;
- change PASS, FAIL, or UNKNOWN;
- convert missing, stale, simulated, or unavailable data into a successful value;
- infer truth from repetition, confidence, visual prominence, hash equality, or agreement alone;
- issue signatures, attestations, capabilities, or authorization tokens;
- trigger Struct Apply or silently repair another component's output.

## 3. Explicit event contract

Every O event MUST contain:
- event_schema_version;
- event_id;
- correlation_id;
- emitter_id and emitter_version;
- event_type;
- sequence_number;
- timestamp and clock_domain;
- observation_status;
- source_refs;
- data_classification;
- diagnostics (bounded and sanitized).

Optional fields MUST be explicitly optional in the schema. Unknown fields MUST NOT be silently reinterpreted as control instructions.

## 4. Read-only enforcement

Read-only is an enforced permission boundary, not a naming convention.

Implementation requirements:
1. Give O read-only credentials or immutable event-stream subscriptions.
2. Do not provide O with write-capable handles to application stores, proof ledgers, authorization services, or source devices.
3. Enforce permissions at the storage/API boundary, not only in O application code.
4. Test attempted writes and require explicit denial.
5. Record access failures without retrying through a more privileged identity.
6. Separate O's own operational logs from the evidence being observed.

A code-level read_only = true flag alone is insufficient.

## 5. Plain-language logic; no obfuscation

All decision logic that affects data acceptance, verification, permissions, state transitions, or application MUST be explicit and reviewable.

Prohibited for security- or correctness-relevant logic:
- minified or intentionally compressed source as the only maintained source;
- opaque aliases that conceal domain meaning;
- encoded decision tables or dynamically assembled rules without a readable source representation;
- hidden side effects inside getters, serializers, callbacks, UI render functions, or telemetry handlers;
- implicit truthiness, default-success fallbacks, swallowed exceptions, or silent coercion;
- reflection or dynamic dispatch where a direct, inspectable mapping is practical;
- a UI label or badge that implies a stronger state than the underlying typed result.

Required:
- descriptive names for state, permission, evidence, and transition fields;
- explicit branches for PASS, FAIL, and UNKNOWN;
- named functions for each validation and authorization step;
- visible error handling and explicit failure outputs;
- comments explaining security invariants, not comments that substitute for tests;
- formatting and linting that preserve readable source;
- tests for each state transition and each denied operation.

If logic is inlined, it MUST remain readable, side-effect-bounded, and equivalent to a named function with a testable contract. Inlining MUST NOT conceal a permission check, validation step, network request, mutation, or error path. Prefer extracting security-critical logic into named functions.

## 6. Reference state model

O observation health is separate from PC2 verification state.

Observation health:
- AVAILABLE: observation received and within declared freshness bounds;
- STALE: observation exists but exceeds its freshness bound;
- MISSING: expected observation was not received;
- UNAVAILABLE: source or observer could not provide the observation;
- CONFLICTING: observations disagree and the disagreement is retained.

PC2 verification:
- PASS: every required verification check passed;
- FAIL: a required check found a defined violation;
- UNKNOWN: required evidence or verification is unresolved or unavailable.

O may report the PC2 verification state emitted by the authorized verifier. O MUST NOT calculate or overwrite that state.

## 7. Explicit handling algorithm

The following is normative pseudocode. It describes O's observation behavior, not proof validation.

    function record_observation(event):
        if event does not match the event schema:
            return REJECTED_SCHEMA

        if event emitter is missing or unidentified:
            return REJECTED_SOURCE

        if event exceeds configured size limits:
            return REJECTED_SIZE

        sanitized_event = remove prohibited sensitive fields(event)
        append sanitized_event to the observer-owned append-only store
        return RECORDED

The observer-owned store is not the proof ledger. Recording an event does not make the event true or verified.

## 8. Deep UI contract

The UI MUST display separate fields:
- inference_status: NOT_STARTED, CANDIDATE, COMPLETED, or ERROR;
- verification_status: PASS, FAIL, or UNKNOWN;
- authorization_status: NOT_EVALUATED, ALLOWED, or BLOCKED;
- observation_health: one of the observation-health states above.

The UI MUST display the emitting component, version, timestamp, and evidence references for important status claims. It MUST NOT infer verification from a successful model/API response, animation, confidence score, cached display, or O event.

## 9. Interline and Crossline

Interline defines each layer's permitted inputs, outputs, side effects, and authority. O receives published observations through explicit read-only interfaces.

Crossline compares independent execution paths or verification results. O can display comparison outcomes but cannot declare the comparison valid. The verifier must define the comparison projection and preserve provenance differences.

For online/offline comparisons:
- compare canonical semantic content and the same parser/rule-set versions;
- retain transport and source-authenticity differences;
- do not equate equal hashes with equal source authenticity;
- return UNKNOWN when required comparison evidence is unavailable.

## 10. Acceptance tests

The O implementation is not conformant until tests demonstrate:
1. O can read allowed event records.
2. O cannot mutate canonical envelopes, ASTs, evidence, proof records, SFTR results, or authorization state.
3. Every attempted prohibited write is denied at the permission boundary.
4. Missing, stale, conflicting, simulated, and unavailable observations remain distinguishable.
5. Observer outage does not silently change verification or authorization.
6. Invalid events are rejected with explicit reason codes.
7. UI status fields remain independent and do not promote candidate inference to verified output.
8. Inlined decision logic has readable source, explicit branches, and equivalent tests.
9. Sensitive raw sensor payloads are not copied into observer logs unless a separately approved, documented requirement permits it.

## 11. Non-claims

This specification does not prove that O is implemented, read-only, air-gapped, cryptographically secure, or production-ready. Those properties require executable tests, deployment configuration review, and runtime evidence.
