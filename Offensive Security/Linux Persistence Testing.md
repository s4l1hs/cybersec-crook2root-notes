---
title: "Linux Persistence Testing"
tags: [tree/offensive, cyber/offensive/post-exploitation/linux-persistence]
Domain: "[[Post-Exploitation & Persistence]]"
Color: "#DC143C"
---

# Linux Persistence Testing

Linux persistence includes user/system services, scheduled execution, login/session hooks, authentication changes, package/application extensions, and cloud-init/container mechanisms.

```mermaid
flowchart LR
    A["Canary"] --> C["cron/systemd/profile/SSH"]
    C --> T["Trigger"]
    T --> L["Logs & detection"]
    L --> R["Rollback"]
```

Assess SSH `authorized_keys`, cron/anacron, systemd units/timers, shell profiles, PAM, sudoers, dynamic linker configuration, package hooks, kernel modules, web application startup, containers/orchestrators, and account changes. High-risk mechanisms such as PAM or kernel modules belong only in disposable ranges.

```shell-session
analyst@range:~$ systemctl list-timers --all | head
NEXT LEFT LAST PASSED UNIT ACTIVATES
```

Use visible inert markers and remote audit. Mastery lab: validate SSH, cron, systemd, and profile canaries, then prove complete removal through file, process, account, and log checks.

---
> 🔼 Up: [[Post-Exploitation & Persistence]]

## Core Concept

**Linux Persistence Testing** is the atomic learning objective of this note. Identify its trust boundary, prerequisites, attacker-controlled input or state, vulnerable transformation, violated security invariant, minimum evidence, business consequence, and safe stopping point. The mechanism must remain explainable without depending on a specific product.

## Visual Attack Flow

```mermaid
flowchart LR
    A["Scoped prerequisite"] --> B["Linux Persistence Testing mechanics"]
    B --> C["Trust boundary crossed"]
    C --> D["Bounded canary proof"]
    D --> E["Detection, remediation & retest"]
```

## Practical Payloads & Execution

```text
fixture=Linux Persistence Testing
build=instrumented-debug
action=trigger minimized canary input
impact_ceiling=fixed marker or deterministic crash
```

### Expected output

```text
result=C2R_CANARY_PROOF
mitigation_state=recorded
production_boundary_crossed=false
```

Treat the output as proof of only the stated condition. Repeat with a negative control, preserve timestamps and the affected build, and never expand impact merely because another path appears reachable.

## Real-World Scenario

A vendor-owned instrumented build reproduces **Linux Persistence Testing**. The researcher demonstrates only a fixed marker or deterministic crash, records architecture and mitigation assumptions, patches the root defect, and retains the minimized input as a regression test.

The durable remediation belongs at the authoritative enforcement layer. Retest the original condition and meaningful variants while verifying legitimate workflows still function.
