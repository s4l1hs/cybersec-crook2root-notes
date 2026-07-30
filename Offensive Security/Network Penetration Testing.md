---
title: Network Penetration Testing
aliases:
  - Network Pentesting
  - Infrastructure Security Testing
tags:
  - tree/offensive
  - cyber/moc
Domain: "[[Penetration Testing]]"
Color: "#DC143C"
---

# Network Penetration Testing

> [!abstract] Lifecycle branch
> External and internal infrastructure testing from attack-surface discovery through service analysis, vulnerability verification, privilege escalation, lateral movement, segmentation testing, and evidence-backed remediation.

```mermaid
flowchart LR
    A["Asset and trust map"] --> R["Recon and enumeration"]
    R --> V["Vulnerability verification"]
    V --> F["Controlled foothold"]
    F --> E["Escalation and lateral movement"]
    E --> I["Impact, cleanup, retest"]
```

## Master notes

- [[Passive Reconnaissance & OSINT]]
- [[Active Reconnaissance & Port Scanning]]
- [[Service Enumeration]]
- [[Vulnerability Scanning & Automation]]
- [[Manual Vulnerability Verification]]
- [[External Network Pentesting]]
- [[Internal Network Pentesting]]
- [[Privilege Escalation & Living off the Land]]

## Typical evidence flow

```text
Asset register → service matrix → verified weaknesses → controlled proof
              → trust-path map → remediation owners → targeted retest
```

---
> 🔼 Up: [[Penetration Testing]]
