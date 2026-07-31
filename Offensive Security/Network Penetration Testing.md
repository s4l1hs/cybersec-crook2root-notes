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
> External and internal infrastructure testing focused on network trust, exposed services, segmentation, remote access, and service-specific attack paths. Reconnaissance and vulnerability assessment have their own lifecycle branches.

```mermaid
flowchart LR
    A["Network trust map"] --> L["Layer 2 and 3 controls"]
    L --> S["Service attack surfaces"]
    S --> F["Controlled foothold"]
    F --> G["Segmentation validation"]
    G --> I["Impact, cleanup, retest"]
```

## 🗺️ Zero-to-Mastery Learning Path

1. [[External Network Pentesting]]
2. [[Internal Network Pentesting]]
3. [[Service Enumeration]]
4. [[Layer 2 Network Attacks]]
5. [[Layer 3 Network Attacks]]
6. [[Enterprise Service Exploitation]]
7. [[Network Segmentation Testing]]
8. [[Remote Access Security Testing]]

## Typical evidence flow

```text
Trust map → service matrix → controlled proof → segmentation path
          → business impact → remediation owners → targeted retest
```

---
> 🔼 Up: [[Penetration Testing]]
