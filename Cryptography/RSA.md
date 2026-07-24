---
title: "RSA"
aliases: ["RSA", "RSA Encryption", "RSA Weak Keys"]
tags:
  - tree/crypto
  - cyber/crypto/asymmetric
  - type/technique
  - level/operator
Domain:
  - "[[Asymmetric Encryption]]"
Color: "#FFE119"
---

# 🔐 RSA

> [!abstract] Leaf of [[Asymmetric Encryption]]
> **RSA** is the classic public-key algorithm. Its security rests on one hard problem: **factoring a large number** back into the two primes that made it. Easy to multiply, brutally hard to reverse — that asymmetry *is* the cryptography.

## Key generation (the whole scheme in five lines)
1. Pick two large primes **p, q**.
2. **n = p × q** (the modulus, the public part everyone sees).
3. **φ(n) = (p−1)(q−1)**.
4. Choose public exponent **e** (usually 65537).
5. Compute private exponent **d = e⁻¹ mod φ(n)**.

Public key = `(n, e)` · Private key = `(d)`. Encrypt: `c = mᵉ mod n`. Decrypt: `m = cᵈ mod n`. Signing is the same with the keys swapped.

## Where it's used
Key exchange in older TLS, SSH host keys, PGP, code/document signing, and JWT `RS256` tokens.

## How RSA falls (CTF & audit favourites)
- **Small `e` (e=3) with no padding** → if the message is tiny, `mᵉ < n` and you just take the cube root.
- **Shared/related primes across keys** → a common `p` between two moduli means `gcd(n1, n2)` factors both instantly.
- **`p` and `q` too close** → **Fermat factorisation** cracks it in milliseconds.
- **Textbook RSA (no OAEP padding)** → malleable and deterministic. Real RSA uses **OAEP** (encryption) and **PSS** (signatures).

```python
# CTF reflex: given n across two keys, try the shared-factor break
from math import gcd
p = gcd(n1, n2)          # if > 1, both keys are broken
```

> [!warning] Crook → Root
> **Crook** trusts "RSA" as a magic word. **Root** checks the *parameters*: key size (≥2048), proper padding, unique well-generated primes. RSA done wrong is RSA broken.

---
> 🔼 Up: [[Asymmetric Encryption]]
