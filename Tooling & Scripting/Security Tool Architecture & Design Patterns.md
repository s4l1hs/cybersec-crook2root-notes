---
title: "Security Tool Architecture & Design Patterns"
aliases: ["Security Tool Design Patterns"]
tags: [tree/tooling, cyber/tooling/development, level/master]
Domain: "[[Writing Your Own Tools]]"
Color: "#708090"
---

# Security Tool Architecture & Design Patterns

A reliable security tool is a controlled evidence pipeline, not a pile of payloads. Its architecture must make scope, rate, identity, provenance, failure, and rollback visible.

```mermaid
flowchart LR
    CLI["CLI / API"] --> VAL["Scope validator"]
    VAL --> PLAN["Immutable execution plan"]
    PLAN --> ENG["Bounded engine"]
    ENG --> PARSE["Typed parser"]
    PARSE --> STORE["Append-only evidence"]
    ENG --> AUDIT["Audit log"]
    STORE --> REPORT["Reporter"]
```

## Core patterns

| Pattern | Purpose | Failure it prevents |
|---|---|---|
| Command pattern | Represent each operation as validated data | Hidden side effects |
| Adapter | Isolate external APIs and protocols | Vendor lock-in and brittle tests |
| Strategy | Swap scan or parse behavior | Giant conditional logic |
| Producer/consumer | Bound concurrency with queues | Resource exhaustion |
| Circuit breaker | Stop after repeated dependency failure | Runaway retries |
| Append-only event log | Preserve provenance and replay | Unverifiable findings |
| Capability boundary | Separate privileged helpers | Whole-process compromise |

## Execution manifest

```json
{
  "run_id": "20260730T090000Z-6e8c",
  "operator": "assessment-team",
  "scope": ["192.0.2.0/28"],
  "exclusions": ["192.0.2.7/32"],
  "rate_per_second": 50,
  "tool_version": "1.4.2",
  "dry_run": false
}
```

Validate CIDRs before execution, make exclusions higher precedence than inclusions, and hash the manifest into every result. Structured logs should go to stderr; machine-readable findings should go to stdout or a named output file.

```shell-session
operator@lab:~$ secprobe plan --config run.yaml --dry-run
PLAN OK  targets=14 excluded=1 max_rate=50/s operations=42
operator@lab:~$ secprobe run --config run.yaml --output results.jsonl
RUN COMPLETE attempted=42 succeeded=40 failed=2 duration=4.81s
```

## Security and quality gates

Threat-model SSRF, command injection, parser bombs, unbounded decompression, credential leakage, path traversal, and malicious target responses. Fuzz every parser boundary. Redact secrets before logs, use explicit timeouts, and support cancellation without corrupting output. A dry run must resolve exactly what would happen without contacting targets.

```mermaid
flowchart TD
    C["Commit candidate"] --> U["Unit + property tests"]
    U --> F["Fuzz parsers"]
    F --> S["SAST + dependency scan"]
    S --> I["Disposable integration lab"]
    I --> B["SBOM, signed binary, checksums"]
```

## Domain model & boundaries

Represent targets, credentials, observations, findings, artifacts, and run metadata as distinct types. An observation is what a sensor saw; a finding is an analyst-supported risk claim. Keeping them separate prevents a banner match from silently becoming “critical vulnerability.”

Use ports/adapters for time, filesystem, network, credential provider, and evidence sink. The core engine should run against fakes without network or disk. Privileged packet capture or host inspection belongs in a minimal helper process with a narrow protocol.

## Resilience patterns

- Idempotency keys prevent repeated side effects after retries.
- Exponential backoff with jitter protects dependencies, but bounded total runtime prevents endless retry.
- Bulkheads isolate one target/protocol pool from exhausting all workers.
- Circuit breakers stop requests after repeated systemic failure.
- Checkpoints permit resume from immutable plans without repeating completed operations.
- Atomic output plus append-only journals preserves partial runs.

## Configuration precedence

Document precedence such as defaults → config file → environment → CLI. Secrets should come from a credential provider, never diagnostic dumps. Print an effective redacted configuration before contact.

```shell-session
operator@lab:~$ secprobe config show --effective --redact
scope=[192.0.2.0/28] exclusions=[192.0.2.7/32] rate=20/s workers=8 token=<redacted>
```

## Observability

Emit structured logs, metrics, traces, and immutable evidence with one run ID. Minimum metrics include planned/completed/failed operations, in-flight workers, rate-limiter wait, latency, retries, bytes, parser failures, and output errors. Avoid high-cardinality target labels in central metrics.

## Crook2Root engineering project

Build a safe HTTP-header auditor with target allowlist, exclusions, DNS revalidation, TLS verification, bounded concurrency, JSONL results, redaction, dry run, cancellation, retries, tests, SBOM, and signed release. Threat-model malicious responses and demonstrate parser fuzzing. A second operator must reconstruct every result from the manifest and evidence.

---
> 🔼 Up: [[Writing Your Own Tools]]
