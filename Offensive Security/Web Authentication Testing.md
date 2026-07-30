---
title: "Web Authentication Testing"
aliases: ["Authentication Attacks", "Identity and Session Testing"]
tags:
  - tree/offensive
  - cyber/offensive/web
  - cyber/identity
  - type/methodology
  - level/root
Domain: "[[Web Application Penetration Testing]]"
Color: "#DC143C"
---

# Web Authentication Testing

> [!abstract] Enterprise methodology
> Authentication testing evaluates account lifecycle, credential verification, MFA, federation, session management, recovery, reauthentication, and abuse resistance. The objective is to prove whether an attacker can become or remain another identity—not merely whether a login form rejects one password.

## Identity lifecycle

```mermaid
flowchart LR
    E["Enroll"] --> V["Verify identity"]
    V --> A["Authenticate"]
    A --> S["Create session"]
    S --> R["Reauthenticate / recover"]
    R --> X["Revoke / terminate"]
    X --> E
```

Test every transition and alternate channel: web, mobile, API, SSO, support desk, invitation, recovery, and device enrollment.

## Enumeration and credential controls

Compare valid and invalid identities across status, body, timing, headers, and side effects:

```http
POST /login HTTP/1.1
Content-Type: application/json

{"email":"known-test@example.com","password":"incorrect-test-value"}

HTTP/1.1 401 Unauthorized
{"error":"Invalid credentials"}
```

Responses should not reveal whether the account exists. Check signup, recovery, invitation, MFA enrollment, and support workflows—not just login.

Rate limiting must consider account, source, device, network, credential pair, and distributed patterns. Use synthetic accounts, agreed request ceilings, and lockout monitoring.

## MFA testing

Verify:

- MFA applies to every login and sensitive action.
- Recovery cannot downgrade assurance silently.
- Backup codes are one-time, protected, and revocable.
- New device enrollment requires existing high-assurance proof.
- Remember-device tokens are scoped, expiring, and invalidated.
- Push workflows resist fatigue and show transaction context.
- Federation and legacy protocols cannot bypass MFA.

Representative state flaw:

```text
1. Test account completes password step → temporary session S1
2. Before MFA, S1 requests /account/export
Expected: 401 or MFA-required
Actual:   200 with synthetic account export
```

## Session management

Inspect cookie/token properties, rotation, logout, concurrency, fixation, idle/absolute timeout, audience, issuer, and revocation.

```http
Set-Cookie: session=<opaque>; Secure; HttpOnly; SameSite=Lax; Path=/
```

After login or privilege change, the session identifier should rotate. After password reset, account disablement, or logout, relevant sessions and refresh tokens should be invalidated.

```mermaid
sequenceDiagram
    participant U as User
    participant A as Auth service
    participant P as Application
    U->>A: credentials + MFA
    A-->>U: short-lived session + refresh token
    U->>P: session
    P->>A: validate issuer, audience, expiry, state
    A-->>P: identity and assurance level
```

## Federation

For OAuth/OIDC and SAML, verify redirect allowlists, state/nonce binding, issuer/audience, signature validation, key selection, code replay resistance, PKCE, account linking, and logout. Use a client-controlled identity provider or test tenant; never tamper with production users.

## Password reset and account recovery

Assess token entropy, single use, expiry, channel security, identity proof, session invalidation, notification, and support overrides. A secure login can be defeated by a weak help-desk reset process.

## Evidence

```text
Test identity: auth-test-02
Assurance expected: password + phishing-resistant MFA
Path tested: legacy mobile API
Actual: password-only token accepted by web API
Impact: MFA bypass for any known credential
Cleanup: token revoked; account reset
```

Remediation should unify policy at the identity service, enforce assurance level server-side, remove legacy exceptions, and add detection for unusual enrollment, recovery, and token use.

---
> 🔼 Up: [[Web Application Penetration Testing]]
