---
title: "Stream Ciphers"
aliases: ["Stream Cipher", "ChaCha20", "RC4", "Keystream", "Nonce Reuse"]
tags:
  - tree/crypto
  - cyber/crypto/symmetric
  - type/technique
  - level/operator
Domain:
  - "[[Branch Symmetric Encryption]]"
Color: "#FFE119"
---

# 🌊 Stream Ciphers

> [!abstract] Leaf of [[Branch Symmetric Encryption]]
> A **stream cipher** generates a pseudo-random **keystream** from the key + a nonce and XORs it with the plaintext byte by byte. Fast, no padding, great for real-time data — but with one unforgiving rule.

## How it works
`ciphertext = plaintext ⊕ keystream(key, nonce)`. Decryption is the same XOR with the same keystream. Because it's just XOR, everything hinges on the keystream being **unique and unpredictable**.

| Cipher | Status |
| --- | --- |
| **ChaCha20(-Poly1305)** | ✅ Modern default — fast in software, used in TLS & WireGuard |
| **RC4** | ❌ Broken — biased keystream; banned from TLS |
| **A5/1** (GSM) | ❌ Legacy, broken |

## The fatal bug: keystream / nonce reuse
Encrypt two messages with the **same key + nonce** and you reuse the keystream:
```
C1 = P1 ⊕ K
C2 = P2 ⊕ K
C1 ⊕ C2 = P1 ⊕ P2     ← key cancels out; attacker recovers plaintext relationships
```
The key vanishes and an attacker can start peeling apart the plaintexts (this is exactly the "two-time pad" break, and it's how WEP Wi-Fi fell). **A nonce must never repeat under the same key.**

> [!warning] Crook → Root
> **Crook:** "It's a random-looking stream, it's safe." **Root:** the cipher is fine; the *operational* rule — never reuse a nonce, always authenticate (Poly1305) — is the whole game. Nonce reuse is a catastrophic, real-world finding.

---
> 🔼 Up: [[Branch Symmetric Encryption]]
