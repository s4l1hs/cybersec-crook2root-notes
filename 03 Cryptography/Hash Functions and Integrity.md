---
title: "Hash Functions & Integrity"
aliases: ["Hash Functions", "Hashing Algorithms", "MD5", "SHA-256", "HMAC", "Avalanche Effect", "Integrity"]
tags:
  - tree/crypto
  - cyber/crypto/hashing
  - type/technique
  - level/apprentice
Domain:
  - "[[Branch Hashing & Passwords]]"
Color: "#FFE119"
---

# #️⃣ Hash Functions & Integrity

> [!abstract] Leaf of [[Branch Hashing & Passwords]]
> A cryptographic hash maps **any input** to a **fixed-length**, one-way digest. It's the fingerprint of data — change one bit and the fingerprint changes completely. This is the backbone of integrity checks, signatures, and (with a KDF) password storage.

## The three properties that make a hash "good"
- **Deterministic** — same input always gives the same digest.
- **Avalanche effect** — flipping one input bit flips ~half the output bits, so digests reveal nothing about their input.
- **Collision resistance** — it's infeasible to find two inputs with the same digest.

```
sha256("Crook2Root")  → 4b8f...e21a
sha256("Crook2root")  → 9c02...77d5   ← one letter, totally different digest
```

## The family (and what's broken)
| Algorithm | Output | Status |
| --- | --- | --- |
| **MD5** | 128-bit | ❌ collisions trivial — integrity only, never security |
| **SHA-1** | 160-bit | ❌ broken (SHAttered) — being retired |
| **SHA-256 / SHA-3** | 256-bit+ | ✅ current standard for integrity & signatures |

## Integrity in practice
```bash
sha256sum ubuntu.iso            # compare to the vendor's published digest
```
If the digests match, the download wasn't corrupted or tampered with. But a plain hash proves integrity only if the *reference* digest is trustworthy — an attacker who can change the file can change the posted hash too.

## HMAC — keyed integrity
Plain hashes don't prove **who** made them. **HMAC** mixes a secret key into the hash (`HMAC(key, message)`), so only someone with the key can produce or verify the tag. It authenticates API requests, webhooks, and messages — integrity *plus* authenticity.

> [!warning] Crook → Root
> **Crook** uses MD5 "because it's a hash." **Root** knows MD5/SHA-1 are integrity relics, reaches for SHA-256+, and uses **HMAC** whenever the tag must also prove origin.

---
> 🔼 Up: [[Branch Hashing & Passwords]]
