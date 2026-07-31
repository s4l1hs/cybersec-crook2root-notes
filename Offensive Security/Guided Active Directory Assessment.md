---
title: Guided Active Directory Assessment
aliases:
  - Enterprise Directory Assessment Walkthrough
tags:
  - tree/offensive
  - cyber/offensive/guided
Domain: "[[Guided Assessments]]"
Color: "#DC143C"
---

# Guided Active Directory Assessment

> [!warning] Authorized enterprise exercise
> Directory testing can affect authentication, replication, and account lockout. Use supplied test identities, approved collection methods, and explicit limits for coercion, relay, password auditing, and persistence simulation.

## Objective

Determine whether ordinary enterprise access can be transformed into control of sensitive systems or directory tiers. The assessment models relationships among identities, endpoints, service accounts, trusts, certificates, delegation, policy, and administrative boundaries.

```mermaid
flowchart TD
    S["Scope forests, domains & tiers"] --> B["Establish low-privilege baseline"]
    B --> G["Collect directory relationships"]
    G --> H["Form attack-path hypotheses"]
    H --> V["Validate one edge at a time"]
    V --> P["Demonstrate bounded privilege"]
    P --> C["Cleanup & control verification"]
```

## 1. Define directory-specific controls

Document forests, domains, trust directions, privileged tiers, certificate authorities, identity providers, endpoint-management systems, domain controllers, and crown-jewel servers. Clarify whether password spraying, NTLM relay simulation, delegation changes, certificate enrollment, or ticket forgery simulation is prohibited.

```text
Test principal: CORP\assessment.user
Permitted: LDAP reads, one approved service-ticket request, ACL validation
Prohibited: production account lockout, DC exploitation, persistent directory objects
Canary group: CORP\Tier0-Test-Admins
Cleanup owner: Identity Engineering
```

## 2. Establish the baseline

Capture the test identity’s group memberships, logon rights, reachable systems, authentication protocols, and assigned workstation. Validate clock synchronization and DNS because Kerberos and directory discovery depend on both. Preserve a before-state for every object that might be changed.

## 3. Map relationships

Collect only the directory properties required to evaluate attack paths: group nesting, sessions where approved, local administrator relationships, service principal names, delegation flags, certificate templates, access-control entries, stale privileged accounts, and cross-domain trusts. Treat graph output as a hypothesis generator, not proof of exploitability.

```text
[edge] assessment.user -> Helpdesk-L1          type=MemberOf
[edge] Helpdesk-L1 -> Workstation-042          type=AdminTo
[edge] Workstation-042 -> svc.deploy           type=SessionCandidate
[edge] svc.deploy -> Server-Admins              type=WriteMember
```

## 4. Validate edges safely

For each path, test prerequisites and effective permissions without immediately exercising the final privilege. Confirm inheritance, deny entries, object protection, protocol availability, endpoint hardening, and whether the supposed session is current. A stale relationship is not a finding.

| Edge class | Safe evidence | Key defensive question |
|---|---|---|
| Excessive ACL | Effective-rights calculation + reversible test object | Why can this role modify a privileged object? |
| Delegation | Account flags + service scope | Is delegation constrained to necessary services? |
| Certificate template | Enrollment rights + issuance settings | Can requesters choose an identity or dangerous purpose? |
| Local admin reuse | Approved endpoint proof | Are local credentials unique and protected? |

## 5. Demonstrate bounded impact

Use a canary group, synthetic service, or disposable test host. Stop at the first proof that establishes the privilege boundary failure. Do not dump unrelated credentials or modify production policy merely to strengthen a finding. Capture event logs and identity-provider telemetry alongside offensive evidence so detections can be evaluated.

## 6. Analyze control layers

Assess tiering, privileged access workstations, local administrator password management, service-account lifecycle, NTLM restrictions, LDAP signing and channel binding, certificate-template governance, delegation review, endpoint credential isolation, and alert coverage. Explain which layer should have prevented each validated edge.

## 7. Restore and retest

Remove test memberships, certificates, tickets, scheduled actions, and temporary objects. Compare the after-state to the baseline. Retesting should show the path is severed at a durable control point and that the intended administrative workflow still works.

---
> 🔼 Up: [[Guided Assessments]]

## Core Concept

**Guided Active Directory Assessment** is the atomic learning objective of this note. Identify its trust boundary, prerequisites, attacker-controlled input or state, vulnerable transformation, violated security invariant, minimum evidence, business consequence, and safe stopping point. The mechanism must remain explainable without depending on a specific product.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Scoped prerequisite"] --> B["Guided Active Directory Assessment mechanics"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, remediation & retest"]
```

## Practical Payloads & Execution

```text
engagement_action=Guided Active Directory Assessment
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

A regulated enterprise assessment applies **Guided Active Directory Assessment** to a canary asset. Scope, evidence provenance, decision points, control outcomes, cleanup, ownership, and retest criteria are recorded so another qualified operator can reproduce the conclusion.

The durable remediation belongs at the authoritative enforcement layer. Retest the original condition and meaningful variants while verifying legitimate workflows still function.
