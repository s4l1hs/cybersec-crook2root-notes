---
title: Guided Social Engineering Exercise
aliases:
  - Human Risk Exercise Walkthrough
tags:
  - tree/offensive
  - cyber/offensive/guided
Domain: "[[Guided Assessments]]"
Color: "#DC143C"
---

# Guided Social Engineering Exercise

> [!danger] Human safety is the primary control
> Obtain executive, legal, HR, privacy, communications, and security approval. Never target personal crises, protected characteristics, medical themes, payroll distress, or other coercive pretexts. Provide a rapid opt-out and do not shame participants.

## Objective

Measure whether people, identity procedures, messaging controls, help desks, physical controls, and incident reporting interrupt realistic influence attempts. The exercise evaluates systems and processes—not employee worth.

```mermaid
flowchart TD
    G["Governance & safety review"] --> H["Threat hypothesis"]
    H --> C["Cohort & control design"]
    C --> E["Controlled execution"]
    E --> S["SOC & help-desk observation"]
    S --> D["Debrief & participant care"]
    D --> I["Control improvement & retest"]
```

## 1. Establish governance

Define target population, exclusions, communication channels, data minimization, retention, success criteria, escalation paths, union or works-council obligations, and the maximum action a participant may take. Use synthetic documents, harmless landing pages, and reversible test identities.

```text
Exercise ID: SE-2026-Q3
Channels: corporate email + help-desk calls
Excluded: interns, leave-of-absence staff, executive assistants
Maximum action: click + synthetic form submission
Never collect: real passwords, MFA tokens, personal data
Immediate stop: distress report, media inquiry, safety concern
```

## 2. Form a threat hypothesis

Tie the scenario to observed enterprise risk, such as supplier impersonation, collaboration invitations, password-reset social pressure, or visitor tailgating. Define the defensive behaviors expected at each layer: mail filtering, link isolation, identity verification, user reporting, SOC triage, and containment.

## 3. Design controls and comparison groups

Use representative cohorts and avoid tiny groups that identify individuals. Establish a control baseline, randomize timing where appropriate, and separate delivery, view, click, data-entry, report, and help-desk escalation metrics. Raw click rate alone is an inadequate measure.

## 4. Execute safely

Launch in bounded waves so the team can stop quickly. Seed monitoring with known test indicators. Exercise messages should be plausible enough to test controls but contain no malware, credential relay, or uncontrolled external dependency. During voice exercises, operators follow a script and terminate on distress or disclosure of sensitive personal information.

```text
10:00 wave=pilot delivered=25 blocked=18 opened=5 reported=4
10:15 SOC case created; indicator correlated across mail gateway
10:22 help desk rejected synthetic recovery request; callback policy followed
```

## 5. Measure the system

Evaluate preventive control rate, median report time, report quality, analyst triage time, containment time, identity-verification adherence, repeat-reporting behavior, and false-positive burden. Segment results by process or control—not by naming individual participants.

## 6. Debrief and improve

Provide immediate reassurance, explain the observable cues, and give one clear reporting action. Share aggregate results. Convert findings into technical and procedural work: stronger sender controls, safer recovery flows, improved reporting buttons, help-desk scripts, and SOC playbooks.

## 7. Retest

Repeat the same threat hypothesis after remediation with changed wording and timing. Improvement means faster detection and safer process execution, not merely recognition of a familiar template.

---
> 🔼 Up: [[Guided Assessments]]

## Core Concept

**Guided Social Engineering Exercise** is the atomic learning objective of this note. Identify its trust boundary, prerequisites, attacker-controlled input or state, vulnerable transformation, violated security invariant, minimum evidence, business consequence, and safe stopping point. The mechanism must remain explainable without depending on a specific product.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Scoped prerequisite"] --> B["Guided Social Engineering Exercise mechanics"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, remediation & retest"]
```

## Practical Payloads & Execution

```text
engagement_action=Guided Social Engineering Exercise
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

A regulated enterprise assessment applies **Guided Social Engineering Exercise** to a canary asset. Scope, evidence provenance, decision points, control outcomes, cleanup, ownership, and retest criteria are recorded so another qualified operator can reproduce the conclusion.

The durable remediation belongs at the authoritative enforcement layer. Retest the original condition and meaningful variants while verifying legitimate workflows still function.
