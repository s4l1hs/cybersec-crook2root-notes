---
title: "TLS & PKI"
aliases: ["TLS", "SSL", "PKI", "HTTPS", "Certificate Authority", "Chain of Trust"]
tags:
  - tree/crypto
  - cyber/crypto/applied
  - type/technique
  - level/operator
Domain:
  - "[[Branch Applied Trust & Tooling]]"
Color: "#FFE119"
---

# 🔒 TLS & PKI

> [!abstract] Leaf of [[Branch Applied Trust & Tooling]]
> **TLS** is where every primitive in this tree comes together to secure a connection — the `S` in HTTPS. **PKI** (Public Key Infrastructure) is the system of trust that makes it work at internet scale.

## The handshake (hybrid crypto in action)
TLS is the textbook example of **hybrid encryption**:
1. **Authenticate** — the server presents a **certificate** (its public key, signed by a CA). The client verifies the signature chain.
2. **Key exchange** — client and server run **ECDHE** to agree a fresh shared secret (asymmetric, with forward secrecy).
3. **Bulk encryption** — the session switches to fast **symmetric AES-GCM** using that secret.
4. **Integrity** — every record carries an authentication tag.

So: **asymmetric** proves identity and agrees a key, **symmetric** does the heavy lifting, **hashing/signatures** guarantee integrity. One connection, every branch of this tree.

## PKI — the chain of trust
A certificate is only meaningful because something trustworthy **signed** it:
```
Root CA  ──signs──▶  Intermediate CA  ──signs──▶  example.com's cert
(in your OS/browser trust store)
```
Your device ships with a set of trusted **Root CAs**. A certificate is valid only if it chains up to one of them, matches the hostname, and hasn't expired or been revoked.

## Where it fails (findings)
- **Self-signed / expired / hostname-mismatch** certs → the browser warning exists for a reason.
- **Weak protocol versions** (SSLv3, TLS 1.0) or **weak ciphers** → downgrade attacks.
- **Trusting a rogue CA** → an attacker-added root enables full HTTPS interception (how corporate/MITM proxies work).

> [!tip] Root move
> `openssl s_client -connect host:443` dumps the cert chain, protocol, and cipher — your first move when auditing a TLS endpoint.

---
> 🔼 Up: [[Branch Applied Trust & Tooling]]
