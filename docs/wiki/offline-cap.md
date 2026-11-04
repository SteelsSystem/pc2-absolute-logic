# Offline CAP

## Definition

The offline profile is a supported execution mode that does not require internet connectivity, remote inference, remote telemetry, runtime CDN assets or online package resolution.

Offline means **no external network dependency at runtime**, not merely “works after the first online launch.”

## Required behavior

- Canonical schema, parser, circular evaluator and SFTR rules are locally available.
- All required dependencies are pinned and present before the offline run.
- Local source ingestion and local persistence are explicit.
- No DNS lookup, analytics, remote logging, telemetry export, CDN fetch or remote model call occurs.
- Missing local resources return a typed unavailable/error result; they are not replaced by defaults.
- A/B/C/O separation remains identical to the online profile.
- Raw biometric/sensor data is ephemeral by default; persistence requires explicit opt-in and retention policy.
- Local clock domains and monotonic timestamps are used for ordering; wall-clock time is metadata only.

## Offline profile manifest

The build/release should declare:
- target platform and architecture;
- enabled features;
- dependency lockfile digest;
- schema and parser versions;
- model/artifact digests if any;
- local storage policy;
- allowed loopback endpoints, if any;
- prohibited egress policy;
- test evidence and build provenance.

## Verification

Required tests:
1. Run with network interfaces disabled or an enforced network namespace/firewall deny policy.
2. Fail the test if any attempted external connection occurs.
3. Confirm no DNS or remote asset fetch is attempted.
4. Confirm parsing and SFTR operate on local canonical fixtures.
5. Confirm no hidden dependency download occurs.
6. Confirm telemetry storage remains local and follows retention settings.
7. Confirm unavailable sensor/device results remain unavailable.
8. Confirm the exact same canonical fixture yields the same AST and SFTR result as online mode.
9. Confirm no online-only module is loaded into the offline process.
10. Confirm observer reports egress-denial events without modifying PC2 state.

A green UI indicator, a configuration flag or an assertion in a README is not proof of offline operation. The test must observe and enforce network denial.
