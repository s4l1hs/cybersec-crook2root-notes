---
title: "AES & Block Ciphers"
aliases: ["AES", "Block Cipher", "Rijndael", "Advanced Encryption Standard"]
tags:
  - tree/crypto
  - cyber/crypto/symmetric
  - type/technique
  - level/apprentice
Domain:
  - "[[Branch Symmetric Encryption]]"
Color: "#FFE119"
---

# 🧱 AES & Block Ciphers

> [!abstract] Leaf of [[Branch Symmetric Encryption]]
> A **block cipher** encrypts fixed-size chunks of data under a shared key. **AES** (Advanced Encryption Standard, née Rijndael) is the workhorse behind disk encryption, TLS, VPNs, and archive passwords — fast, hardware-accelerated, and unbroken when used correctly.

## The essentials
- **Block size:** 128 bits (16 bytes). Data longer than one block needs a **mode** (next leaf); data shorter needs **padding**.
- **Key sizes:** 128, 192, or 256 bits — more rounds for bigger keys (10 / 12 / 14). AES-128 is still considered strong; AES-256 is the common default for "future-proof."
- **Symmetric:** the *same* key encrypts and decrypts, so the whole security model depends on getting that key to the other side safely (solved by asymmetric key exchange).

## Where you meet it
| Use | Example |
| --- | --- |
| Data at rest | LUKS, BitLocker, FileVault |
| Data in transit | TLS (`AES_256_GCM`), SSH, WireGuard |
| Archives | AES-encrypted ZIP/7z |

## Why the cipher is rarely the weak point
AES itself has no practical break. Real findings come from **how** it's used:
- Wrong **mode** (ECB) → pattern leakage.
- Hard-coded or reused **keys/IVs** in source or config.
- No **authentication** (using raw CBC instead of GCM) → tampering and padding-oracle attacks.

> [!tip] Root move
> When you see AES in a target, don't attack the cipher — attack the **key management** and the **mode**. That's where the crypto actually fails.

---
> 🔼 Up: [[Branch Symmetric Encryption]]
