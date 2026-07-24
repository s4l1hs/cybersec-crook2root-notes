---
title: "Digital Signatures"
aliases: ["Digital Signatures", "Digital Signature", "Signing", "Non-repudiation"]
tags:
  - tree/crypto
  - cyber/crypto/asymmetric
  - type/technique
  - level/operator
Domain:
  - "[[Branch Asymmetric Encryption]]"
Color: "#FFE119"
---

# ✍️ Digital Signatures

> [!abstract] Leaf of [[Branch Asymmetric Encryption]]
> A signature flips asymmetric crypto around: you **sign with the private key** and anyone **verifies with the public key**. It proves a message is **authentic** (from the key's owner), **intact** (unmodified), and gives **non-repudiation** (the signer can't deny it).

## Hash-then-sign
Signing the whole message with slow asymmetric maths is wasteful, so we **hash first**, then sign the digest:
```
signature = encrypt_with_private( hash(message) )
verify    = decrypt_with_public(signature) == hash(message)   → authentic ✓
```
If a single byte of the message changes, its hash changes, and verification fails. This is why signatures deliver integrity *and* authenticity in one move. Common algorithms: **RSA-PSS**, **ECDSA**, **Ed25519**.

## Why it's everywhere
| Signed thing | What the signature guarantees |
| --- | --- |
| **TLS certificates** | a CA vouches this public key belongs to this domain |
| **JWTs** | the token was issued by the server and not tampered with |
| **Software / commits** | the code came from the real author (code signing, GPG) |
| **Documents** | legal non-repudiation |

## Where it breaks
- Verifying with the **wrong / attacker-supplied key** (the JWT `alg:none` and key-confusion bugs).
- **Not verifying at all** — accepting a token/cert because it merely *has* a signature field.
- Signing over the wrong data (signature covers the header but not the payload).

> [!warning] Crook → Root
> **Crook** sees a signature and assumes trust. **Root** asks: *signed by which key, verified against which trusted anchor, covering exactly what bytes?* That's the difference between real authenticity and theatre.

---
> 🔼 Up: [[Branch Asymmetric Encryption]]
