---
title: "XOR & Classical Ciphers"
aliases: ["XOR", "XOR Cipher", "ROT13", "Caesar Cipher", "Vigenere", "Classical Ciphers"]
tags:
  - tree/crypto
  - cyber/crypto/encoding
  - type/technique
  - level/apprentice
Domain:
  - "[[Encoding & Obfuscation]]"
Color: "#FFE119"
---

# ⊕ XOR & Classical Ciphers

> [!abstract] Leaf of [[Encoding & Obfuscation]]
> These are **reversible obfuscation** dressed up as encryption. They appear constantly in CTFs, malware string-hiding, and legacy junk — and a **Root** breaks them by hand.

## XOR
XOR (`⊕`) is the building block of real stream ciphers, but *by itself with a short/reused key* it's trivially broken. Its defining property: `A ⊕ B ⊕ B = A` — applying the same key twice restores the original.
```python
# single-byte XOR "encryption"
cipher = bytes(b ^ 0x2A for b in b"secret")
# brute force: only 256 keys, so try them all and look for readable text
for k in range(256):
    out = bytes(c ^ k for c in cipher)
```
- **Single-byte XOR** → 256 keys → brute force instantly.
- **Repeating-key XOR (Vigenère-style)** → find the key length (Kasiski / index of coincidence), then solve each position as single-byte XOR.
- **One-Time Pad** is the *only* unbreakable XOR — key as long as the message, truly random, never reused. Reuse a keystream and it collapses (see stream-cipher nonce reuse).

## Substitution ciphers
- **Caesar / ROT-N** — shift each letter by N. **ROT13** is its own inverse. 25 keys → brute force.
- **Vigenère** — Caesar with a repeating keyword; broken by finding the keyword length, then frequency analysis per column.

> [!warning] Crook → Root
> **Crook:** "It's shifted, nobody can read it." **Root:** classical ciphers have negligible keyspace and leak language statistics — they're puzzles, not protection. Any real secret needs a modern cipher with a proper key.

---
> 🔼 Up: [[Encoding & Obfuscation]]
