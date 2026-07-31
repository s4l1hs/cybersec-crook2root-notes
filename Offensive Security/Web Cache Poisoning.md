---
title: "Web Cache Poisoning"
tags: [tree/offensive, cyber/offensive/atomic, level/master]
Domain: "[[HTTP Architecture & Advanced Web Attacks]]"
Color: "#DC143C"
---

# Web Cache Poisoning

> [!warning] Authorized simulation only
> Use only inside an explicitly scoped laboratory or enterprise assessment. Prefer synthetic identities, canary objects, reversible proof, and minimum necessary impact.

## Core Concept

**Web Cache Poisoning** is an atomic discipline within **HTTP Architecture & Advanced Web Attacks**. Mastery means identifying the controlling trust boundary, prerequisite identity, attacker-controlled value, vulnerable transformation, violated invariant, and smallest reproducible evidence. Do not confuse a product signature with the underlying mechanism.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Controlled input"] --> B["Web Cache Poisoning condition"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, repair & retest"]
```

## Practical Payloads & Execution

The example uses documentation-only targets and synthetic data. Preserve the raw action, response, UTC timestamp, identity, affected build, and cleanup state.

```http
GET / HTTP/1.1
X-Forwarded-Host: canary.untrusted.example
```

### Expected vulnerable output

```text
X-Cache: MISS
Next normal request: X-Cache: HIT
reflected host: canary.untrusted.example
```

The output proves only the stated condition. Repeat with a negative-control identity or input and record the secure expected result; do not expand impact merely because additional access appears possible.

## Real-World Scenario

In a multi-tenant enterprise service, an authorized assessor discovers web cache poisoning on a synthetic customer workflow. The proof uses one canary object and stops before accessing production data.

The operator correlates application, identity, endpoint, and network telemetry where applicable, removes every test artifact, and identifies the earliest durable control that would sever the attack path.

## Defensive Engineering & Retest

Fix the root cause at the authoritative enforcement layer, add positive and negative regression cases, minimize exposed privilege, produce high-quality audit events, and test equivalent routes, encodings, legacy versions, asynchronous workers, caches, and alternate clients. Closure requires the original proof and meaningful variants to fail while legitimate workflows continue to work.

## Crook2Root Mastery Checklist

- Explain the mechanics independently of tools.
- State prerequisites, invariant, evidence, and impact ceiling.
- Reproduce the canary action and expected output.
- Map preventive, detective, and recovery controls.
- Clean up all artifacts and define a durable retest condition.

---
> 🔼 Up: [[HTTP Architecture & Advanced Web Attacks]]
