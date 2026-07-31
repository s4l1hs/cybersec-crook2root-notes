---
title: "File, Parser & Serialization Security"
tags: [tree/offensive, cyber/offensive/web/parsers, cyber/moc]
Domain: "[[Web Application Penetration Testing]]"
Color: "#DC143C"
---

# File, Parser & Serialization Security

```mermaid
flowchart LR
    U["Upload / path / document"] --> P["Parser"]
    P --> O["Object or filesystem"]
    O --> E["Execution / disclosure / mutation"]
```

## 🗺️ Zero-to-Mastery Learning Path

1. [[Path Traversal]]
2. [[Local File Inclusion]]
3. [[Remote File Inclusion]]
4. [[File Upload Security Testing]]
5. [[XML External Entity Testing]]
6. [[Insecure Deserialization Testing]]

---
> 🔼 Up: [[Web Application Penetration Testing]]
