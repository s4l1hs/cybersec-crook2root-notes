---
title: Guided Cloud Security Assessment
aliases:
  - Enterprise Cloud Assessment Walkthrough
tags:
  - tree/offensive
  - cyber/offensive/guided
Domain: "[[Guided Assessments]]"
Color: "#DC143C"
---

# Guided Cloud Security Assessment

> [!warning] Shared-responsibility scope
> Confirm the cloud provider’s penetration-testing policy, tenant and subscription identifiers, regions, approved identities, managed-service restrictions, and third-party boundaries before testing.

## Objective

Assess whether cloud identities, control-plane policy, network exposure, workload identity, data services, automation, and logging prevent an initial low-privilege principal from reaching protected resources.

```mermaid
flowchart TD
    S["Accounts, tenants & subscriptions"] --> I["Identity baseline"]
    I --> C["Control-plane inventory"]
    C --> E["Exposure & trust analysis"]
    E --> P["Privilege-path validation"]
    P --> D["Data-plane proof"]
    D --> L["Logging, cleanup & retest"]
```

## 1. Define the cloud boundary

Record organizations, management groups, accounts, projects, subscriptions, regions, virtual networks, clusters, identity tenants, CI/CD systems, SaaS integrations, and data classifications. Distinguish provider-managed infrastructure from customer-controlled configuration.

```text
Provider: multi-cloud
Authorized tenants: tenant-sec-test, account 123456789012
Principal: assessment-reader@example.test
Allowed: read inventory, test synthetic role, inspect public exposure
Excluded: provider infrastructure, resource exhaustion, real secret retrieval
```

## 2. Establish identity truth

Capture the effective principal, federation source, token audience, claims, role assignments, policy boundaries, session duration, conditional access, and strong-authentication status. Test with supplied identities rather than creating unknown access paths.

## 3. Inventory the control plane

Enumerate authorized resource metadata and normalize it into identities, policies, networks, compute, containers, serverless functions, data stores, secrets, keys, logging, and automation. Review both direct permissions and trust relationships. Infrastructure-as-code is valuable evidence, but deployed state is authoritative.

## 4. Model attack paths

Look for privilege created through combinations: pass-role plus function update, instance profile plus metadata access, public workload plus overprivileged service identity, CI secret plus deployment rights, or cross-account trust plus weak external conditions. Prove each edge independently.

```text
Path hypothesis:
assessment-reader -> read function configuration
function role -> read synthetic secret
update permission -> absent
Result: visibility finding, not privilege escalation
```

## 5. Validate exposure and data controls

Check public endpoints, storage policies, database networking, security groups, firewall rules, private endpoints, encryption keys, snapshots, registries, and backup access. Use canary records and metadata-only proof. Verify that tenant boundaries hold across API, console, object URLs, and asynchronous jobs.

## 6. Evaluate workload identity

Assess metadata-service protections, pod or task identities, service-account binding, secret injection, build runners, deployment agents, and lateral trust between workloads. Determine whether workloads receive only task-specific privileges and whether short-lived credentials replace static secrets.

## 7. Verify detection and recovery

Correlate controlled policy denials, role assumptions, public-policy changes on a test resource, secret access, and unusual API calls with centralized audit telemetry. Confirm logs are organization-controlled, immutable, region-complete, and monitored.

## 8. Cleanup and retest

Remove test resources, policies, keys, snapshots, public rules, federated sessions, and automation artifacts. Compare inventory before and after. Retest the repaired policy and adjacent identities so closure demonstrates least privilege without breaking legitimate deployment flows.

---
> 🔼 Up: [[Guided Assessments]]

## Core Concept

**Guided Cloud Security Assessment** is the atomic learning objective of this note. Identify its trust boundary, prerequisites, attacker-controlled input or state, vulnerable transformation, violated security invariant, minimum evidence, business consequence, and safe stopping point. The mechanism must remain explainable without depending on a specific product.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Scoped prerequisite"] --> B["Guided Cloud Security Assessment mechanics"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, remediation & retest"]
```

## Practical Payloads & Execution

```text
engagement_action=Guided Cloud Security Assessment
asset=C2R-CANARY
identity=authorized-test-principal
impact_ceiling=single synthetic proof
```

### Expected output

```text
expected_output=C2R_CANARY_PROOF
cleanup=verified
retest_condition=recorded
```

Treat the output as proof of only the stated condition. Repeat with a negative control, preserve timestamps and the affected build, and never expand impact merely because another path appears reachable.

## Real-World Scenario

A regulated enterprise assessment applies **Guided Cloud Security Assessment** to a canary asset. Scope, evidence provenance, decision points, control outcomes, cleanup, ownership, and retest criteria are recorded so another qualified operator can reproduce the conclusion.

The durable remediation belongs at the authoritative enforcement layer. Retest the original condition and meaningful variants while verifying legitimate workflows still function.
