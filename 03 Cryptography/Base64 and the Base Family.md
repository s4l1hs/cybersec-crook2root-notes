---
title: "Base64 & the Base Family"
aliases: ["Base64", "Base32", "Base58", "Base Encoding"]
tags:
  - tree/crypto
  - cyber/crypto/encoding
  - type/technique
  - level/crook
Domain:
  - "[[Branch Encoding & Obfuscation]]"
Color: "#FFE119"
---

# 🅱️ Base64 & the Base Family

> [!abstract] Leaf of [[Branch Encoding & Obfuscation]]
> **Base64** re-expresses arbitrary bytes using 64 printable characters so binary can ride through text-only channels (email, JSON, URLs, data-URIs). It is **encoding, not encryption** — reversible instantly with no key.

## How it works
Base64 takes **3 bytes (24 bits)** and splits them into **4 groups of 6 bits**, mapping each to `A–Z a–z 0–9 + /`. When the input isn't a multiple of 3 bytes, the output is padded with `=`.
- One trailing `=` → input was 2 bytes short of a triple; `==` → 1 byte. Trailing `=` is the classic **fingerprint** that says "this is Base64."
- **URL-safe Base64** swaps `+ /` for `- _` so it survives inside URLs (used in JWTs).

## The Base family
| Scheme | Alphabet size | Seen in |
| --- | --- | --- |
| **Base64** | 64 (`A-Za-z0-9+/`) | email, JWT, data-URIs, config blobs |
| **Base32** | 32 (`A-Z2-7`) | TOTP/2FA secrets, onion addresses |
| **Base58** | 58 (no `0 O l I`) | Bitcoin addresses, avoids look-alikes |

## Do it in a terminal
```bash
echo -n "Crook2Root" | base64            # Q3Jvb2syUm9vdA==
echo "Q3Jvb2syUm9vdA==" | base64 -d      # Crook2Root
```
The **Hashsmith** CLI adds auto-detection: `hashsmith decode --auto "Q3Jvb2syUm9vdA=="`.

> [!warning] Security note (Crook → Root)
> **Crook:** "It looks scrambled, so it's safe." **Root:** Base64 provides *zero* confidentiality — a secret in a Base64 blob is a secret in plaintext. If you ever need secrecy, you need **encryption** (a key), not encoding. Seeing credentials Base64-encoded in traffic or config is a finding, not a mitigation.

---
> 🔼 Up: [[Branch Encoding & Obfuscation]]
