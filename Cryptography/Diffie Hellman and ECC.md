---
title: "Diffie–Hellman & ECC"
aliases: ["Diffie-Hellman", "Diffie Hellman", "DH", "ECC", "Elliptic Curve", "ECDH", "Key Exchange"]
tags:
  - tree/crypto
  - cyber/crypto/asymmetric
  - type/technique
  - level/operator
Domain:
  - "[[Asymmetric Encryption]]"
Color: "#FFE119"
---

# 🤝 Diffie–Hellman & ECC

> [!abstract] Leaf of [[Asymmetric Encryption]]
> **Diffie–Hellman (DH)** lets two parties agree on a **shared secret over a public channel** — an eavesdropper who sees every message still can't compute the secret. It's the magic trick that makes HTTPS possible without pre-sharing keys.

## The idea (paint-mixing analogy)
Both sides publicly agree on a common colour, each mixes in a **private** colour, they exchange the mixtures, then each adds their private colour again. Both arrive at the same final mixture; an observer can't "un-mix" to recover it. Mathematically it's **modular exponentiation**: shared `g, p`; Alice sends `gᵃ mod p`, Bob sends `gᵇ mod p`; both compute `g^(ab) mod p`. The attacker sees `gᵃ` and `gᵇ` but the **discrete-log problem** stops them getting `g^(ab)`.

## ECC — same idea, smaller keys
**Elliptic-Curve Cryptography** achieves equal security with far shorter keys (a **256-bit** ECC key ≈ a **3072-bit** RSA key), which means less CPU and bandwidth — why modern TLS uses **ECDHE** and phones prefer ECC. Curve25519 is the popular workhorse.

## The catch: authentication
Raw DH is vulnerable to a **man-in-the-middle** — the attacker does a separate exchange with each side. DH gives *secrecy*, not *identity*. Real protocols pair it with **signatures/certificates** (next leaf) to prove who's on the other end. **Ephemeral** DH (the `E` in ECDHE) also gives **forward secrecy**: one stolen long-term key can't decrypt past sessions.

> [!warning] Crook → Root
> **Crook:** "They exchanged keys in the open — that's insecure!" **Root:** DH is *designed* for that; the real risk is an **unauthenticated** exchange (MITM), fixed by binding it to a verified identity.

---
> 🔼 Up: [[Asymmetric Encryption]]
