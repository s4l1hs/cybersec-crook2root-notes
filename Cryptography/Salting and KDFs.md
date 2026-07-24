---
title: "Salting & KDFs"
aliases: ["Salting", "Salt", "KDF", "Key Derivation Function", "bcrypt", "scrypt", "Argon2", "Password Storage"]
tags:
  - tree/crypto
  - cyber/crypto/hashing
  - cyber/defense
  - type/technique
  - level/operator
Domain:
  - "[[Hashing & Passwords]]"
Color: "#FFE119"
---

# 🧂 Salting & KDFs

> [!abstract] Leaf of [[Hashing & Passwords]]
> This is the single most important **defensive** note in the tree: how to store passwords so a database breach isn't game over. The answer is a **salt** plus a **slow, memory-hard KDF** — never a bare fast hash.

## Why a plain `sha256(password)` fails
- **Fast** — a GPU computes billions of SHA-256s per second, so a leaked dump of fast hashes cracks quickly.
- **Unsalted** — identical passwords produce identical hashes, and precomputed **rainbow tables** reverse common ones instantly.

## Salt — kill precomputation
A **salt** is a unique, random value stored alongside each hash: `hash(salt + password)`. Now:
- Two users with the same password get **different** hashes.
- Rainbow tables are useless — an attacker must crack each password **individually**.

## KDFs — make each guess expensive
Salting stops precomputation; a **Key Derivation Function** makes every guess *slow*. bcrypt, scrypt, and **Argon2** are deliberately **CPU- and memory-hard**, with a tunable **work factor**:
```python
# ✅ the only correct way to store a password
import bcrypt
bcrypt.hashpw(pw.encode(), bcrypt.gensalt(rounds=12))   # salt is generated & embedded automatically

# ❌ an integrity primitive misused for passwords
hashlib.sha256(pw.encode()).hexdigest()
```
| KDF | Note |
| --- | --- |
| **Argon2id** | modern winner — memory-hard, resists GPU/ASIC (first choice) |
| **scrypt** | memory-hard, solid |
| **bcrypt** | battle-tested, fine with a high work factor |
| **PBKDF2** | acceptable where FIPS is required; weakest of the four |

Tune the work factor so a single hash takes ~250 ms: trivial for one login, ruinous for a billion-guess attack.

> [!warning] Crook → Root
> **Crook** stores `md5(password)`. **Root** stores `argon2id(salt + password)` with a calibrated work factor, and treats crackable hashes in an assessment as a **critical finding** in the storage design.

---
> 🔼 Up: [[Hashing & Passwords]]
