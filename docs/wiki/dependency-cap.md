# Dependency CAP

Here, **CAP means capability envelope and compatibility profile**, not the distributed-systems CAP theorem.

Every dependency must have a machine-readable inventory record. No dependency may be silently optional, silently substituted or implicitly trusted.

## Required inventory fields

| Field | Meaning |
|---|---|
| `dependency_id` | Stable identifier |
| `version` | Exact version or immutable commit |
| `digest` | Verified package/artifact digest |
| `source` | Registry, repository or vendored source |
| `role` | Parser, crypto, storage, sensor adapter, UI, telemetry, test |
| `required_for` | Explicit feature/process stages |
| `offline_profile` | `REQUIRED`, `OPTIONAL`, `FORBIDDEN`, `NOT_APPLICABLE` |
| `online_profile` | `REQUIRED`, `OPTIONAL`, `FORBIDDEN`, `NOT_APPLICABLE` |
| `network_behavior` | None, local-only, explicit remote endpoints |
| `data_access` | Data classes and permissions |
| `failure_policy` | Fail, unknown/block, or safe feature disable |
| `license` | SPDX identifier or documented exception |
| `owner` | Responsible workstream |
| `verification` | Lockfile, digest, SBOM and test evidence |

## Capability classes

- **CORE:** required for deterministic canonicalization, parsing, provenance or SFTR. Must be available in the offline profile.
- **LOCAL_ADAPTER:** hardware/local sensor integration; optional to PC2 logic, but explicitly declared for B telemetry.
- **ONLINE_ADAPTER:** network connector; absent from offline builds and unable to bypass canonical ingress.
- **OBSERVABILITY:** local metrics/logging; must not introduce network egress by default.
- **DEV_TEST:** development and CI only; never linked into production authorization path.
- **VISUALIZATION:** dashboard/theme; no authority over validation state.

## Dependency rules

1. Pin direct and transitive versions using the native lockfile.
2. Verify artifacts by digest; verify signatures where the ecosystem supports trusted signatures.
3. Generate an SBOM for release artifacts.
4. Record licenses and transitive dependencies.
5. Deny undeclared runtime downloads.
6. No remote code loading, CDN runtime dependencies or package installation during offline execution.
7. A dependency failure cannot be converted to `PASS`.
8. Optional dependency failure may disable only its declared feature; required dependency failure blocks that feature.
9. Keep parser, canonicalization, hashing and policy logic minimal to reduce the trusted computing base.
10. Reconcile dependency inventories in CI and flag drift.

## Compatibility matrix

| Dependency category | Offline | Online | PC2 authority |
|---|---|---|---|
| Canonical schema/parser | Required | Required | May produce parse result only |
| Hash/crypto implementation | Required | Required | Supports integrity checks, not source truth |
| Local persistence | Optional/declared | Optional/declared | No authority |
| Remote API client | Forbidden unless explicit offline loopback contract | Optional/declared | No authority |
| EEG/audio/IMU driver | Optional for B | Optional for B | Signal only |
| Circular/Unicircle evaluator | Local deterministic implementation required if feature enabled | Same implementation | Structural/phase observations only |
| Observer | Local read-only core | Same core; remote export opt-in only | No authority |
| UI/chart library | Optional | Optional | No authority |

## Supply-chain acceptance

A release is not dependency-CAP compliant unless it has:
- lockfile and immutable version resolution;
- artifact digest verification;
- SBOM;
- license inventory;
- no undeclared network dependency;
- offline dependency-resolution test;
- dependency-failure behavior test;
- documented exception for every non-reproducible artifact.

A passing dependency audit does not prove application correctness. It establishes bounded and traceable dependency inputs.
