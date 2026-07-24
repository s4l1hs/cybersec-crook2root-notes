---
title: "Block Cipher Modes"
aliases: ["Block Cipher Modes", "ECB", "CBC", "CTR", "GCM", "Modes of Operation", "Authenticated Encryption"]
tags:
  - tree/crypto
  - cyber/crypto/symmetric
  - type/technique
  - level/operator
Domain:
  - "[[Branch Symmetric Encryption]]"
Color: "#FFE119"
---

# 🔀 Block Cipher Modes

> [!abstract] Leaf of [[Branch Symmetric Encryption]]
> A block cipher only encrypts one 16-byte block. The **mode of operation** is how you chain blocks to encrypt a whole message — and choosing wrong is one of the most common, most *visible* crypto failures.

## The modes that matter
| Mode | Idea | Verdict |
| --- | --- | --- |
| **ECB** | Each block encrypted independently | ❌ **Never** — identical plaintext blocks → identical ciphertext blocks |
| **CBC** | Each block XORed with the previous ciphertext + random **IV** | ⚠️ OK but unauthenticated → padding-oracle risk |
| **CTR** | Turns the block cipher into a keystream (counter) | ⚠️ Good, but still needs separate authentication |
| **GCM** | CTR **+ built-in authentication tag** | ✅ **Default** — confidentiality *and* integrity |

## The "ECB penguin"
Encrypt an image with **ECB** and you can still see the picture — because every identical region of pixels encrypts to the identical ciphertext. This single fact is why ECB is a punchline. If you can send known plaintext and watch for repeating 16-byte ciphertext blocks, you've detected ECB — an instant finding.

```
Plaintext blocks:  [AAAA][AAAA][BBBB]
ECB ciphertext:    [ 7f ][ 7f ][ c1 ]   ← repetition leaks structure
```

## Authenticated encryption (the modern default)
Encryption without **integrity** lets an attacker *tamper* with ciphertext (bit-flipping, padding oracles). **AES-GCM** (or ChaCha20-Poly1305) attaches an authentication tag so any tampering is detected. Rule of thumb: **if it's not authenticated, it's not done.**

> [!warning] Crook → Root
> **Crook** picks "AES" and ships it in ECB. **Root** knows the cipher is a footnote — the mode (GCM), a unique IV/nonce per message, and an auth tag are what actually protect the data.

---
> 🔼 Up: [[Branch Symmetric Encryption]]
