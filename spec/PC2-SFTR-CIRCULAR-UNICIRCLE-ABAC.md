# PC2 / SFTR Integration Contract
## Circular Calculus, Unicircle, A/B/C, Offline Telemetry and Safe-Bond Boundary

**Version:** 1.0.0-draft  
**Status:** Architecture contract / implementation baseline  
**Target branch:** `sftr-circular-unicircle-sync-v1`  
**Scope:** PC2 Absolute Logic, CobraLayer/Microlang, SFTR, L-EEG-Personal and Forensica AI Sandbox

This document defines the intended architecture and capability limits. It does not claim that all components are already deployed, connected to hardware, or clinically/forensically validated.

## 1. Non-negotiable boundary — BLOCK THIS

The sandbox and perceptual telemetry must not become proof or authorization authorities.

- A bias score is not a truth score.
- EEG, RGB, audio, IMU, GLSL and other physiological/perceptual telemetry cannot independently prove a proposition, causation, credibility, consent, diagnosis or authorization.
- A SHA-256 digest proves byte identity relative to the bytes hashed; it does not prove source authenticity, sensor validity, semantic truth or completeness.
- Phase-lock, coherence, circularity, drift or consensus metrics cannot authorize PC2 execution.
- Sandbox output cannot write to the PC2 proof ledger, issue an authorization token or bypass SFTR.
- UI color, demo values, default values and badges cannot be represented as live verified state.
- Missing, stale, malformed, uncalibrated or conflicting telemetry must not be replaced with zero, nominal or PASS defaults.

A missing sensor signal is not evidence that a forensic claim is false. It makes that signal unavailable.

## 2. A/B/C architecture and capability caps

For this specification, **CAP** means a bounded capability/authority envelope, not a claim about the distributed-systems CAP theorem.

| System | Role | Capability cap |
|---|---|---|
| **A — Adversarial Verification** | PC2 grammar, AST, provenance, formal inference, authorization and SFTR | Sole authority to PASS, FAIL or block PC2 execution |
| **B — Perceptual Signals** | Local EEG, RGB, audio, IMU, GLSL and device/runtime telemetry | Measure, validate signal quality and report observations only |
| **C — Circular Calculus & Synchronization** | Circular inference-graph analysis, Unicircle phase analysis, clock/sequence alignment and telemetry-joint health | Report structure, alignment, divergence or UNKNOWN; no execution authority |
| **Sandbox** | Isolated lexical, feature and candidate classification research | Candidate indicators only; no writes or grants to A |
| **Magnetic safe-bond** | Passive physical retention/alignment/coupling | Safe-bond only; never a breaker or execution interlock |

Non-interference rules:

- B does not promote a measurement into an A proof.
- C does not promote phase or graph consistency into truth or authorization.
- Sandbox does not mutate A or B state.
- A consumes B/C outputs only as explicitly typed, provenance-labelled observations; no B/C result can satisfy a formal proof obligation by default.

Each system requires separate state namespaces, permission boundaries and result types. A unified dashboard may display their relationship but cannot merge their authority.

## 3. Circular calculus parser

### 3.1 Purpose

The Circular parser analyzes dependencies among proposition, evidence and inference records. It distinguishes:

- a structural graph cycle;
- a declared iterative/feedback process;
- an epistemic cycle in which a conclusion is used as its own sole support;
- an unresolved cycle whose independent roots or provenance cannot be established.

**A graph cycle alone is not proof of circular reasoning.** The edge semantics, provenance and independent source roots matter.

### 3.2 Typed graph input

A graph contains:

- `ClaimNode`: stable claim ID and proposition reference;
- `EvidenceNode`: source reference, raw-source digest, authenticity/verification state, extraction version and location;
- `InferenceNode`: rule ID/version, premise references, conclusion reference and inference-engine version;
- `Edge`: `SUPPORTS`, `DERIVES`, `CONTRADICTS`, `UPDATES` or `OBSERVES`;
- `CycleContext`: evaluation/session ID, declared feedback purpose, version/sequence boundary and independent-root references.

Reject dangling references, duplicate IDs, unknown edge types, invalid provenance, oversized inputs and out-of-bound graphs.

### 3.3 Classification

Use strongly connected component (SCC) analysis and a condensation graph. A cycle is classified as:

- `NO_CYCLE`: no cyclic dependency found.
- `STRUCTURAL_CYCLE`: a cycle exists, but self-supporting inference has not been established.
- `TEMPORAL_FEEDBACK`: an explicitly declared process-feedback loop has distinct, ordered versions and independent observations. This is operational feedback, not proof of a proposition.
- `SELF_SUPPORTING_CYCLE`: a conclusion depends on itself through SUPPORTS/DERIVES edges and no independent admissible evidence root exists within the declared proof boundary.
- `UNRESOLVED_CYCLE`: edge semantics, provenance, ordering or independent roots are incomplete.

A cycle involving proof edges and independent evidence is not automatically invalid; it must be reported as structural and reviewed for the precise inference rule. A feedback loop must never be silently converted into an evidentiary proof chain.

### 3.4 Bounds

Initial evaluator limits, enforced internally:

- maximum dependency/AST depth: 6;
- maximum graph nodes: 1,000;
- maximum graph edges: 4,000;
- maximum serialized input: 1 MiB;
- target structural-evaluation budget: 5 ms on a declared reference profile.

The 5 ms value is a benchmark target, not a universal device guarantee. Timeout or resource exhaustion returns `UNKNOWN` and blocks execution. It is not semantic FAIL. Use bounded iterative traversal where practical; do not depend on unbounded recursion.

## 4. Unicircle calculus parser

### 4.1 Domain

Unicircle is a bounded periodic-signal and phase-analysis component. It is not a truth calculus and does not establish proposition validity.

A stream is not Unicircle-valid just because it can be drawn as a circle. It requires a sample sequence, known units, declared phase extraction, calibration metadata, a shared time base and sufficient signal quality.

### 4.2 Circular operations

For phase `theta`, normalize to `[-pi, pi)`:

`wrap(theta) = ((theta + pi) mod 2pi) - pi`

Circular difference:

`delta(a,b) = wrap(a - b)`

Circular mean:

`mu = atan2(mean(sin(theta_i)), mean(cos(theta_i)))`

Resultant length:

`R = sqrt(mean(cos(theta_i))^2 + mean(sin(theta_i))^2)`

Circular dispersion indicator:

`V = 1 - R`

For two validated, aligned phase series:

`PLV = abs(mean(exp(i * (phi_A_i - phi_B_i))))`

These metrics report phase concentration or consistency only. They do not prove semantic agreement, causal influence, truth, intent or clinical meaning. Record phase-extraction method, window, sample count, filtering parameters, calibration version and quality flags.

### 4.3 Outcomes

- `UNICIRCLE_VALID`: valid periodic signal with sufficient quality and phase reference.
- `UNICIRCLE_DRIFT`: signal exists but phase/frequency drift exceeds the configured tolerance.
- `MULTI_ORBIT`: multiple unresolved phase sources/orbits are mixed.
- `UNRESOLVED`: insufficient samples, stale data, dropped samples, missing phase reference or bad calibration.
- `INVALID_INPUT`: malformed or out-of-bounds data.

Never manufacture a default phase, phase-lock value or nominal verdict when a stream is absent.

## 5. Telemetry joints and synchronization

### 5.1 Packet envelope

Every telemetry packet carries:

- schema version;
- session ID, source ID and stream ID;
- monotonically increasing sequence number;
- monotonic-clock timestamp and declared clock domain;
- optional wall-clock timestamp (annotation only);
- metric name, numeric value(s), unit and sample/window definition;
- sample rate and calibration ID/version;
- quality state, dropped-sample count and freshness;
- data class: `SIMULATED`, `LIVE_UNVERIFIED`, `LIVE_VERIFIED` or `UNAVAILABLE`;
- canonical payload digest and digest algorithm;
- local-retention policy and consent state.

Compute SHA-256 over deterministic canonical serialization. This verifies the serialized packet bytes, not sensor attestation or source authenticity unless a separate authenticated device/source chain establishes those.

### 5.2 Join rules

Join streams only when session and stream identities are explicit, clock domains are known, timestamps fit the configured skew, sequence gaps are reported, units/calibration/windows are compatible and each input is fresh.

Join results:

- `ALIGNED`: all required compatibility checks pass.
- `MISALIGNED`: a defined timing or compatibility invariant is violated.
- `UNKNOWN`: essential metadata or quality is missing.
- `UNAVAILABLE`: source is absent or disabled.

Preserve source identity and uncertainty. Do not average away missing or contradictory observations.

### 5.3 Offline-native operation

The B/C path defaults to local-only operation: no cloud inference, analytics, CDN, remote export or automatic synchronization. Use bounded local queues and local SQLite/Room persistence only when enabled. Raw biometric data remains ephemeral by default; persistence requires explicit opt-in, minimization and a retention rule.

Prove offline operation with network-denial tests. A UI label or a README assertion is not proof of zero egress.

## 6. Sandbox quarantine

Forensica AI Sandbox remains isolated. Its current phrase and heuristic outputs may identify review candidates, not facts.

Allowed output: candidate features, lexical indicators, circularity candidates, source references, model/rule version and uncertainty.

Prohibited output: PC2 `PASS`, proof certificates, authorization tokens, truth/causality claims based solely on score, or direct mutation of A.

The adapter validates a versioned schema and imposes CPU, memory, time, input and output bounds. Crash, timeout, missing output or malformed output becomes `UNKNOWN`.

Source review identified two integration defects to repair before relying on its LOOP_CYCLE output: `nlp/forensic_nlp.py` derives a confidence/pass value from keyword activation and circular-pattern matches; `pipeline/engine.py` calls DEFENSE even if the returned LOOP_CYCLE result is false. Require PASS for final synthesis; FAIL blocks; UNKNOWN routes to review and blocks certification.

## 7. Bias-spectrum task-archetype filter

The exact filename `bias-spectrums-eternal.py` was not located in the accessible repository search/tree scan. Its identity and implementation remain unverified.

Retain a candidate module only if it directly serves the task archetype, documents inputs/features/units/ranges, identifies source and dataset provenance, reports uncertainty and missing-data behavior, has deterministic offline tests, uses bounded resources, has no hidden network dependency and emits observation-only results.

Otherwise label it `OUT_OF_SCOPE_PENDING_REVIEW`. EEG/audio/GLSL visualization can remain in B for its own telemetry purpose; it is not imported into A merely because it contains the word “spectrum”.

## 8. Aether Purple — visual telemetry layer

Aether Purple is a neutral telemetry visualization theme, not a verdict state.

| Token | Value | Use |
|---|---|---|
| `aether-bg` | `#100A1F` | Main background |
| `aether-surface` | `#1A1230` | Panel surface |
| `aether-purple` | `#9B7BFF` | Main signal accent |
| `aether-violet` | `#6F4BCE` | Secondary accent |
| `aether-lilac` | `#D8C8FF` | Data and labels |
| `aether-glow` | `#BCA4FF` | Focus and selected-stream accent |

Color must never be the only state indicator. Include text and symbols, and test contrast/accessibility. SFTR PASS/FAIL/UNKNOWN states remain semantically distinct from telemetry coloring.

## 9. Magnetic safe-bond boundary

**Magnets are safe-bond couplers only — not breakers.**

Allowed: passive retention, alignment or detachable coupling where the mechanical/electrical design independently establishes safe operating limits.

Not allowed: using a magnet, magnetic proximity, magnetic telemetry or a magnetically triggered state as a power breaker, execution interlock, proof signal, emergency stop or authorization condition.

A magnetic coupling must not interrupt power, data integrity or PC2 state transitions by design. Any actual protective shutdown must use a separately engineered and independently tested safety mechanism. Keep magnetic components appropriately separated from sensors or components whose readings they may disturb; validate interference experimentally rather than assuming it is absent.

Magnetic coupling state is telemetry at most: `BONDED`, `UNBONDED` or `UNKNOWN`. It never maps to PC2 PASS.

## 10. Existing-code migration notes

Source-level findings in the inspected `L-EEG-Personal` branch:

- `SpectRoomSensorPathEngine.buildInitialDashboardState()` seeds synthetic values, marks hashes verified and labels paths “NOMINAL LOCK”. Mark these as `SIMULATED`/demo and do not expose them as live state.
- Several Unicritic model defaults begin with high scores before fresh telemetry. Production initial state must be `UNINITIALIZED` or `UNAVAILABLE`.
- `RgbCognitiveMode.TRUTH_GREEN` should be renamed to a signal-only name such as `BALANCE_GREEN`; a color channel is not a truth sensor.
- `UnicriticVerdictLevel.OPTIMAL` must describe technical telemetry health only, not epistemic validity.
- Any statement that EEG-derived stress/load proves coercion or disproves voluntariness must be removed or rewritten as a limited, uncertainty-bearing physiological observation.
- Magnetic components must be passive safe-bond elements only, as specified in Section 9.

These are migration requirements, not changes already committed.

## 11. SFTR result mapping

| Condition | Result | Execution |
|---|---|---|
| Grammar/bounds violation | `FAIL` | Block |
| Provenance mismatch | `FAIL` | Block |
| Self-supporting epistemic cycle | `FAIL` | Block |
| Authorization denied | `FAIL` | Block |
| Missing proof or unresolved cycle | `UNKNOWN` | Human review; block certification |
| Telemetry unavailable | B/C `UNAVAILABLE` | Does not decide A |
| Telemetry misaligned | C `MISALIGNED` | Do not use that joined metric |
| All required A checks independently pass | `PASS` | May proceed under separate authorization policy |

B/C alignment, Unicircle validity, a high signal score, an Aether color or a magnetic bond must never override an A-system result.

## 12. Acceptance tests

1. Same circular graph input produces the same canonical SCC result.
2. A self-supporting claim cycle with no independent evidence root returns `SELF_SUPPORTING_CYCLE`.
3. A structural observation cycle is not mislabeled as proof of circular reasoning.
4. An explicitly declared temporal feedback stream with ordered version metadata is classified separately from an epistemic proof cycle.
5. Graph depth/node/edge/byte limits are enforced internally.
6. Unicircle wrap, difference, mean, resultant length, dispersion and PLV pass numerical reference tests.
7. Unicircle rejects NaN, infinity, empty windows, incompatible sample counts and invalid sample rates.
8. Telemetry digest is recomputed from canonical bytes; mismatch is rejected.
9. Duplicate/out-of-order sequence numbers and stale timestamps are reported.
10. Different clock domains are not joined without an explicit conversion contract.
11. Missing telemetry never defaults to zero, nominal or verified.
12. SIMULATED telemetry is visibly distinct from live telemetry.
13. Sandbox FALSE/FAIL/UNKNOWN never results in certified DEFENSE output.
14. Network-denial test shows B/C works without external egress.
15. Magnetic bond/unbond events never trigger a breaker, authorization or PC2 state change.

## 13. Implementation status

This branch contains the architecture contract. The exact `bias-spectrums-eternal.py` file remains unfound. No claim is made that the production backend, hardware sensors, offline isolation or parser deployment has been verified by this document.
