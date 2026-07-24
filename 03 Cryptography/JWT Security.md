---
title: "JWT Security"
aliases: ["JWT", "JSON Web Token", "alg none", "JWT Attacks"]
tags:
  - tree/crypto
  - cyber/crypto/applied
  - cyber/offensive
  - type/technique
  - level/operator
Domain:
  - "[[Branch Applied Trust & Tooling]]"
Color: "#FFE119"
---

# 🎫 JWT Security

> [!abstract] Leaf of [[Branch Applied Trust & Tooling]]
> A **JSON Web Token** is a signed, self-contained identity token an API trusts on every request. It's hashing and signatures (this tree) applied to authentication — and it's a rich attack surface when the signature is checked carelessly.

## Structure
Three Base64url parts joined by dots: **`header.payload.signature`**
```
eyJhbGciOiJIUzI1NiJ9  .  eyJ1c2VyIjoiYWRtaW4ifQ  .  3x9f...   ← signature over header+payload
   (alg: HS256)              (user: admin)
```
The header and payload are **only encoded** (Base64url) — readable by anyone. The **signature** is what makes it trustworthy: it's an HMAC (`HS256`) or an RSA/ECDSA signature (`RS256`) over the first two parts, so tampering invalidates it.

## The classic attacks
- **`alg: none`** — attacker sets the header algorithm to `none` and strips the signature; a naive server that "verifies" accepts an unsigned token → forge any user.
- **Algorithm confusion (`RS256 → HS256`)** — server expects an RSA-signed token but the attacker signs an `HS256` token using the **public** key as the HMAC secret; a library that picks the algorithm from the token verifies it.
- **Weak `HS256` secret** → crack it offline with a wordlist, then sign arbitrary tokens.
- **Sensitive data in the payload** — it's plaintext Base64, not encrypted.

```bash
# audit reflex: is the signing secret weak?
hashcat -m 16500 jwt.txt rockyou.txt        # crack an HS256 secret offline
```

## Defenses
Pin the expected algorithm server-side, use a **strong random secret** (or proper key management for RS256), always verify the signature, and keep secrets **out** of the payload.

> [!warning] Crook → Root
> **Crook** decodes a JWT, sees `user: admin` is just Base64, and edits it. **Root** knows the edit only works if the server fails to verify the signature — so the *real* target is the verification logic (`alg:none`, key confusion, weak secret).

---
> 🔼 Up: [[Branch Applied Trust & Tooling]]
