# Parallel workstreams and acceptance gates

Work can proceed in parallel only across explicit interfaces. Integrate through the canonical envelope and versioned contracts; do not create parallel definitions of the same schema.

## Workstream 1 — Dependency CAP

Deliverables:
- dependency inventory and lockfiles;
- SBOM and license report;
- offline/online compatibility labels;
- artifact integrity and provenance checks;
- dependency failure matrix.

Exit gate: no undeclared runtime dependencies; offline resolution is reproducible.

## Workstream 2 — Canonical struct and adapters

Deliverables:
- canonical JSON Schema;
- Rust and Python typed representations;
- offline fixture adapter;
- online adapter;
- canonicalization and digest test vectors.

Exit gate: paired online/offline fixture tests produce equivalent canonical payloads and the same parser/SFTR outcome where provenance conditions are equivalent.

## Workstream 3 — PC2/SFTR parser and inference

Deliverables:
- grammar and AST version;
- circular dependency evaluator;
- Unicircle phase evaluator;
- proof/provenance representation;
- tri-state check aggregator.

Exit gate: no execution on FAIL or UNKNOWN; parser syntax is not mislabeled semantic proof.

## Workstream 4 — B telemetry

Deliverables:
- local sensor adapters;
- quality/freshness/calibration metadata;
- missing-data and drift behavior;
- local-only storage profile.

Exit gate: telemetry remains observation-only and unavailable values are never defaulted to zero/PASS.

## Workstream 5 — O Observer

Deliverables:
- event schema;
- read-only collection interface;
- correlation and timing diagnostics;
- bounded local retention;
- observer overhead tests.

Exit gate: Observer cannot mutate proof or authorization state.

## Workstream 6 — Sandbox

Deliverables:
- task-archetype classification;
- typed feature output;
- CPU/memory/time/input/output bounds;
- explicit UNKNOWN behavior;
- no-write adapter into PC2.

Exit gate: sandbox output cannot certify DEFENSE or bypass LOOP_CYCLE.

## Integration sequence

1. Freeze schema/version identifiers and test vectors.
2. Implement adapters and typed structs independently against the schema.
3. Run contract tests in each language.
4. Run offline network-denial tests.
5. Run online adapter trust-boundary tests.
6. Run parser/SFTR property and adversarial tests.
7. Run integration tests across A/B/C/O.
8. Produce SBOM, dependency report and test evidence.
9. Publish Wiki pages and update status only from CI evidence.

## Global acceptance invariants

- One canonical envelope across online/offline ingress.
- One parser/validation policy across both modes.
- No implicit online trust.
- No sensor-to-proof promotion.
- No sandbox-to-authorization path.
- Observer read-only.
- Fail closed for authorization; preserve UNKNOWN as distinct from FAIL.
- Magnetic safe-bond is passive only and is not a breaker.
- All claims of build/test/offline compliance are backed by recorded evidence.
