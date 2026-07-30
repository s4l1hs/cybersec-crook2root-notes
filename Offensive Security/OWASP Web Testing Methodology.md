---
title: "OWASP Web Testing Methodology"
aliases: ["OWASP Web Testing", "Web Assessment Methodology"]
tags:
  - tree/offensive
  - cyber/offensive/web
  - type/methodology
  - level/operator
Domain: "[[Web Application Penetration Testing]]"
Color: "#DC143C"
---

# OWASP Web Testing Methodology

> [!abstract] Enterprise methodology
> A systematic web assessment maps architecture, identities, data flows, controls, and abuse cases before testing vulnerability classes. OWASP provides coverage guidance; professional testing adapts that guidance to business context and proves root causes safely.

## Testing model

```mermaid
flowchart LR
    S["Scope and architecture"] --> M["Map routes, roles, data"]
    M --> I["Identity and session testing"]
    I --> A["Authorization and business logic"]
    A --> D["Input, browser, API, infrastructure"]
    D --> R["Risk, evidence, remediation, retest"]
```

## Application map

Record hosts, reverse proxies, APIs, WebSockets, file stores, identity providers, third parties, admin surfaces, mobile clients, and background workers. Build a route inventory from normal user journeys rather than brute-force alone.

```text
POST /api/orders             customer creates order
GET  /api/orders/{id}        customer reads order
POST /api/orders/{id}/refund manager approves refund
GET  /admin/export           administrator exports records
```

For each route capture method, parameters, content type, authentication, role, object ownership, state change, data classification, and expected invariants.

## OWASP-aligned coverage

| Area | Questions |
|---|---|
| Information gathering | What components, routes, files, and trust boundaries exist? |
| Configuration/deployment | Are debug behavior, headers, TLS, storage, and admin interfaces secure? |
| Identity | Can accounts be enumerated, created, recovered, or linked incorrectly? |
| Authentication | Are credentials, MFA, federation, and reauthentication robust? |
| Authorization | Are functions and objects restricted server-side? |
| Session management | Are tokens unpredictable, rotated, invalidated, and scoped? |
| Input validation | Can data cross into SQL, OS, template, XML, LDAP, or browser execution contexts? |
| Error handling | Do errors disclose internals or alter control flow? |
| Cryptography | Are keys, algorithms, secrets, and transport protections appropriate? |
| Business logic | Can valid features be sequenced or repeated to violate an invariant? |
| Client-side | Can browser trust, DOM, origins, frames, or storage be abused? |
| APIs | Are object, function, property, and resource limits enforced? |

## Baseline and differential testing

Establish a valid request, change one variable, and compare status, body structure, headers, timing, and side effects:

```http
GET /api/orders/4102 HTTP/1.1
Host: app.example.com
Authorization: Bearer <customer-A-test-token>

HTTP/1.1 200 OK
{"id":4102,"owner":"customer-a","total":125.00}
```

Then use a client-provided object owned by customer B. The safe proof is a controlled cross-account record—not real customer data.

## Input-to-sink reasoning

For every parameter identify parsing layers and eventual sink:

```mermaid
flowchart LR
    U["User input"] --> P["Proxy/parser"]
    P --> F["Framework validation"]
    F --> Q["Query/template/OS/XML sink"]
    Q --> O["Response or side effect"]
```

Test encoding and type transitions deliberately. A JSON number becoming a string, or a repeated parameter becoming an array, can bypass assumptions without any exotic payload.

## Evidence

Preserve raw request/response pairs, role and object ownership, time, account state, side effect, cleanup, and expected versus actual behavior. Avoid relying solely on screenshots.

```text
Expected: customer A receives 404 for customer B's test order
Actual:   customer A receives 200 and B's synthetic order metadata
Impact:   horizontal object-level authorization bypass
Cleanup:  test objects deleted through approved admin account
```

## Retesting

Retest the root cause across equivalent routes, methods, encodings, API versions, and roles. Confirm server-side enforcement, not merely UI hiding or one blocked payload.

---
> 🔼 Up: [[Web Application Penetration Testing]]
