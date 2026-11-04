# Observer and telemetry joints

## O — Observer / Observability Plane

Observer is read-only. It records what components report; it does not decide truth, infer authorization or change component state.

Responsibilities:
- correlate event IDs, session IDs, sequence numbers and timestamps;
- record dependency availability, version and digest;
- record parser/SFTR transitions and diagnostics;
- record telemetry freshness, quality, gaps and synchronization;
- expose disagreement between components without collapsing it;
- report its own sampling overhead and dropped events.

Observer must not:
- write to the proof ledger;
- modify A/B/C results;
- create an authorization token;
- change PASS/FAIL/UNKNOWN;
- turn missing observations into zero or nominal values.

## Event envelope

Every observer event should carry:
- event schema version;
- event ID and correlation/session ID;
- emitting component and component version;
- monotonic timestamp and clock domain;
- sequence number;
- event kind and severity;
- referenced source/evidence IDs;
- data classification (simulated/live/unavailable);
- bounded diagnostic payload;
- integrity digest where required.

Avoid copying raw biometric or sensitive source payloads into logs. Prefer references and minimized diagnostic metadata.

## Telemetry joints

A join between streams is valid only when source identity, session, clock domain, timestamp skew, sequence continuity, units, calibration and sample-window definitions are compatible.

Join outcomes:
- `ALIGNED`
- `MISALIGNED`
- `UNKNOWN`
- `UNAVAILABLE`

Do not interpolate missing values unless a named signal-processing operation explicitly requires it and records that it interpolated. Never use an interpolated value as an original observation.

## Aether Purple display tokens

Purple is a visualization theme, not an epistemic state:
- background `#100A1F`
- surface `#1A1230`
- primary `#9B7BFF`
- secondary `#6F4BCE`
- highlight `#D8C8FF`

Use text labels and accessible contrast; never rely on color alone. Do not encode PASS/FAIL/UNKNOWN as decorative telemetry colors.

## Observer failure

Observer failure must not change PC2 authorization. It should be reported as an observability outage. If an audit or retention policy requires an observer record for a particular operation, the policy gate may block that operation explicitly; this must be a named requirement, not an implicit side effect.
