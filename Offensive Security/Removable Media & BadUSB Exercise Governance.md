---
title: "Removable Media & BadUSB Exercise Governance"
tags: [tree/offensive, cyber/offensive/social/removable-media]
Domain: "[[Physical Social Engineering]]"
Color: "#DC143C"
---

# Removable Media & BadUSB Exercise Governance

Removable-media exercises measure device control, autorun/execution prevention, endpoint telemetry, user reporting, and response using inert signed canaries.

```mermaid
flowchart LR
    D["Inventory-tagged device"] --> H["Host control"]
    H --> C["Canary action"]
    C --> T["Telemetry/response"]
    T --> R["Device recovery"]
```

Never deploy destructive keystrokes or uncontrolled payloads. Define serials, placement, collection, expiry, behavior, target cohort, and lost-device procedure. Mastery lab: compare blocked storage, allowed storage, and HID canary behavior in an isolated endpoint range.

## Device classes and controls

USB storage, human-interface devices, network adapters, serial devices, and composite devices present different trust decisions. Blocking mass storage does not prove that a keyboard-emulating device or USB Ethernet adapter is controlled. Assess device-control policy, port restrictions, endpoint telemetry, driver installation, application control, user reporting, and physical asset handling as separate layers.

```text
Asset: C2R-USB-017
Class: signed read-only storage canary
Placement: approved training room
Behavior: opens static instructions only; no auto-execution
Expiry: 18:00 UTC
Recovery owner: Physical Security
```

For HID simulation, use an isolated test endpoint and an inert action such as typing a unique marker into a local text editor. Never launch a shell, alter security controls, or rely on uncontrolled timing. Photograph placement only where privacy rules permit, track every serial, and treat an unrecovered device as an incident. Evaluate whether controls block the class, whether telemetry identifies insertion and process lineage, and whether staff report unknown media without plugging it in.

---
> 🔼 Up: [[Physical Social Engineering]]

## Core Concept

**Removable Media & BadUSB Exercise Governance** is the atomic learning objective of this note. Identify its trust boundary, prerequisites, attacker-controlled input or state, vulnerable transformation, violated security invariant, minimum evidence, business consequence, and safe stopping point. The mechanism must remain explainable without depending on a specific product.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Scoped prerequisite"] --> B["Removable Media & BadUSB Exercise Governance mechanics"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, remediation & retest"]
```

## Practical Payloads & Execution

```text
exercise=Removable Media & BadUSB Exercise Governance
cohort=privacy-approved canary group
maximum_action=harmless marker
real_credentials_collected=false
```

### Expected output

```text
prevented_or_reported=true
participant_harm=none
control_owner=identified
```

Treat the output as proof of only the stated condition. Repeat with a negative control, preserve timestamps and the affected build, and never expand impact merely because another path appears reachable.

## Real-World Scenario

A privacy-approved enterprise exercise applies **Removable Media & BadUSB Exercise Governance** with synthetic identities and a harmless canary action. Legal, HR, privacy, and the white team define prohibited themes and stop conditions; results measure process and control performance rather than individuals.

The durable remediation belongs at the authoritative enforcement layer. Retest the original condition and meaningful variants while verifying legitimate workflows still function.
