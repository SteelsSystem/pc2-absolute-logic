# PC2 / SFTR Wiki

This directory is the version-controlled, Wiki-ready architecture reference for PC2/SFTR. It is kept beside the implementation specification so changes can be reviewed and versioned with code. It is not a claim that GitHub's separate Wiki feature is enabled.

## Governing rule

**Offline and online ingress must normalize into the same canonical process structure before validation.** Transport-specific adapters may differ; canonical fields, schema rules, provenance checks, parser behavior, SFTR gates and authorization policy must not.

`Transport → Adapter → Canonical Envelope → Schema Validation → Provenance Validation → Parser/Inference → SFTR → Authorization`

No transport can bypass a stage.

## Pages

- [Canonical ingress and struct process](canonical-ingress.md)
- [Dependency CAP](dependency-cap.md)
- [Offline CAP](offline-cap.md)
- [Online adapter and trust boundary](online-cap.md)
- [O-Spec: Observer and explicit logic contract](o-spec.md)
- [Observer and telemetry joints](observer-telemetry.md)
- [Parallel workstreams and acceptance gates](parallel-workstreams.md)

## Authority model

- **A — Adversarial verification:** grammar, AST, provenance, inference and SFTR decision.
- **B — Perceptual signals:** local sensor and telemetry observations only.
- **C — Circular/Unicircle analysis:** dependency-cycle, phase and synchronization observations only.
- **O — Observer:** read-only event and dependency observability; no proof or authorization power.
- **Sandbox:** isolated candidate-feature analysis; cannot mutate A or grant execution.

## Status vocabulary

`PASS`, `FAIL`, `UNKNOWN` are reserved for the PC2/SFTR validation decision. Transport and telemetry health use separate vocabularies such as `AVAILABLE`, `UNAVAILABLE`, `STALE`, `MISALIGNED`, `SIMULATED`. Telemetry health must never be substituted for proof validity.

## Current implementation status

This wiki records a proposed contract and implementation plan. Unless a specific test run or deployment artifact is linked, treat the requirements as **not yet verified in production**.
