---
title: "Command & Control Design Principles"
aliases: ["C2 Design Principles"]
tags: [tree/tooling, cyber/tooling/development/c2, level/master]
Domain: "[[Writing Your Own Tools]]"
Color: "#708090"
---

# Command & Control Design Principles

> [!warning] Controlled emulation only
> This chapter concerns transparent, auditable command-and-control emulators for owned cyber ranges and purple-team exercises. It deliberately excludes covert deployment, security-control bypass, persistence, and unauthorized access.

A safe C2 emulator coordinates pre-approved test actions while preserving operator identity, target consent, command provenance, cryptographic integrity, and immediate revocation.

```mermaid
sequenceDiagram
    participant O as Authorized operator
    participant A as Audited control API
    participant Q as Signed task queue
    participant G as Lab agent
    participant L as Central audit log
    O->>A: Submit allowlisted action
    A->>L: Record identity, scope, approval
    A->>Q: Sign short-lived task
    G->>Q: Poll with mutual TLS
    G->>G: Validate signature, scope, expiry
    G->>L: Stream start/result/cleanup events
```

## Control-plane requirements

- Mutual TLS with short-lived workload identities; no shared fleet secret.
- Role-based authorization and a second approval for high-impact lab actions.
- Allowlisted task schemas rather than arbitrary shell strings.
- Signed tasks containing agent ID, scope, nonce, issue/expiry time, and maximum runtime.
- Per-agent and global kill switches tested before exercises.
- Immutable, centralized audit events that endpoints cannot erase.
- A cleanup action and success predicate for every state-changing emulation.

```json
{
  "task_id": "sim-1042",
  "agent_id": "range-win-07",
  "action": "create_detection_marker",
  "args": {"marker": "C2R-PURPLE-1042"},
  "expires": "2026-07-30T10:05:00Z",
  "max_runtime_seconds": 20
}
```

```shell-session
operator@range:~$ c2lab submit task.json --approval CHG-20481
accepted task=sim-1042 agent=range-win-07 expires_in=287s
operator@range:~$ c2lab events sim-1042
10:00:13Z validated  signature=ok scope=ok
10:00:14Z completed  marker=C2R-PURPLE-1042
10:00:15Z cleaned    verification=absent
```

## Failure design

Agents must fail closed on stale tasks, clock skew beyond tolerance, unknown action types, scope mismatch, invalid signatures, and unavailable audit sinks. Queues need idempotency keys so retries do not duplicate side effects. Treat telemetry loss as an exercise stop condition.

## Purple-team value

The emulator should produce a published behavior catalog mapped to expected data sources and detections. Replaying a stable, transparent action makes defensive regression testing possible without hiding from the defenders being trained.

## Component model

Separate identity provider, operator API, policy engine, task signer, queue, agent, artifact store, and audit sink. No component should both authorize and erase its own evidence. Agents accept typed capabilities, not general shell commands.

## Task lifecycle

```mermaid
stateDiagram-v2
    [*] --> Draft
    Draft --> Approved
    Approved --> Signed
    Signed --> Leased
    Leased --> Running
    Running --> Completed
    Running --> Failed
    Running --> Cancelled
    Completed --> Cleaned
    Failed --> Cleaned
    Cancelled --> Cleaned
    Cleaned --> [*]
```

Leases prevent two agents from executing one task. Nonces prevent replay. Expiry limits stale commands. Idempotency keys make retries safe. Every transition emits an immutable event.

## Agent hardening

Run as an unprivileged dedicated identity, sandbox action plugins, pin server trust, rotate workload certificates, cap CPU/memory/runtime/output, and keep the update mechanism signed and separate. Configuration must define lab identifiers and refuse unknown environments. Local spooling may bridge brief audit outages, but policy decides whether execution can continue; high-impact actions should fail closed.

## API & data model

Use authenticated requests with operator, approval, target, action schema, purpose, expiry, and cleanup. Results contain status, bounded structured output, artifact hashes, and verification—not arbitrary unbounded stdout.

## Purple-team master lab

Build an emulator with two benign actions: create/remove a canary file and generate an approved DNS marker. Demonstrate mutual TLS, RBAC, second approval, signed task, replay rejection, cancellation, audit outage behavior, remote telemetry, cleanup, and detection regression. Threat-model malicious operator, compromised agent, stolen queue token, and clock skew.

## Governance

Publish behavior catalog, expected data sources, change approvals, release signatures, emergency shutdown, retention, and exercise report. The architecture must make simulated behavior attributable and reviewable; concealment and security-control bypass are explicitly out of scope.

---
> 🔼 Up: [[Writing Your Own Tools]]
