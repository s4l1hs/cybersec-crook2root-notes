---
title: "Hashing & Passwords"
aliases: ["Hashing and Passwords", "Hashing", "Password Storage"]
tags:
  - tree/crypto
  - cyber/crypto/hashing
  - type/concept
  - level/apprentice
Domain:
  - "[[Cryptography]]"
Color: "#FFE119"
---

# #️⃣ Hashing & Passwords

> [!abstract] Branch ④ of [[Cryptography]]
> A hash is a fixed-length, **one-way** fingerprint — no key, not reversible. It powers integrity checks and password storage. This branch takes you from *what a hash is* (Crook) to *cracking a leaked hash dump and then fixing the storage* (Root).

The defining split: **fast hashes** (SHA-256) are for **integrity**; **slow, salted KDFs** (bcrypt/Argon2) are for **passwords**. Confusing the two is the single most common fatal storage mistake.

```mermaid
flowchart LR
    I["Any input"] -->|"one-way hash"| D["Fixed-length digest"]
    D -.->|"cannot reverse"| I
    style D fill:#14351a,stroke:#51cf66,color:#d3f9d8
```

## The leaves in this branch
- ****Hash Functions and Integrity**** — MD5/SHA family, the avalanche effect, and **HMAC** for keyed integrity.
- ****Salting and KDFs**** — why salt defeats rainbow tables and why bcrypt/scrypt/**Argon2** are deliberately slow.
- ****Password Cracking**** — offline attacks with **Hashcat** and **John**, wordlists, and mangling rules.

> [!tip] Root move
> If a target's hashes crack in seconds, *the password storage is the finding.* The fix lives in ****Salting and KDFs****.

## 📄 Notes in this branch
- [[Hash Functions and Integrity]]
- [[Salting and KDFs]]
- [[Password Cracking]]

---
> 🔼 Up: [[Cryptography]]
