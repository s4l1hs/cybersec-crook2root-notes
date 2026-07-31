---
title: "Client-Side Web Security"
tags: [tree/offensive, cyber/offensive/web/client-side, cyber/moc]
Domain: "[[Web Application Penetration Testing]]"
Color: "#DC143C"
---

# Client-Side Web Security

Browser security depends on origins, execution contexts, DOM sinks, cookies, navigation, framing, storage, and cross-origin policy.

```mermaid
flowchart TD
    U["Untrusted source"] --> D["DOM / JavaScript"]
    D --> X["Execution sink"]
    B["Browser security model"] --> D
    P["CSP / SameSite / CORS"] --> B
```

## 🗺️ Zero-to-Mastery Learning Path

1. [[Cross-Site Scripting]]
2. [[CSRF & SameSite Testing]]
3. [[CORS Misconfiguration]]
4. [[Clickjacking]]
5. [[Prototype Pollution & DOM Security]]

---
> 🔼 Up: [[Web Application Penetration Testing]]
