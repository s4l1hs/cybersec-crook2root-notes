---
title: "C2 Infrastructure & Operational Security"
tags: [tree/offensive, cyber/offensive/red-team/c2, cyber/moc]
Domain: "[[Red Team Operations]]"
Color: "#DC143C"
---

# C2 Infrastructure & Operational Security

```mermaid
flowchart LR
    O["Operator"] --> C["Control plane"]
    C --> R["Redirector"]
    R --> A["Canary agent"]
    C --> L["Immutable audit"]
```

## 🗺️ Zero-to-Mastery Learning Path

1. [[Command & Control Architecture]]
2. [[Redirectors, Domains & Traffic Governance]]
3. [[Red Team Operational Security & Teardown]]
4. [[Anti-Forensics Methodology]]

---
> 🔼 Up: [[Red Team Operations]]
