---
title: "Password Cracking"
aliases: ["Password Cracking", "Hashcat", "John the Ripper", "Wordlists", "rockyou", "Cracking Rules"]
tags:
  - tree/crypto
  - cyber/crypto/hashing
  - cyber/offensive
  - type/technique
  - level/operator
Domain:
  - "[[Hashing & Passwords]]"
Color: "#FFE119"
---

# 💥 Password Cracking

> [!abstract] Leaf of [[Hashing & Passwords]]
> Cracking is **offline** guessing: you have the hashes and try candidate passwords until a hash matches. It's how you demonstrate weak storage in an assessment — and why **salting & KDFs** matter.

> [!warning] Authorized Simulation only
> Crack hashes **only** in an authorized assessment, a CTF, or against your own systems. Cracking others' credentials is illegal.

## The two industry tools
```bash
# Hashcat — GPU-accelerated;  -m selects the hash mode
hashcat -m 0    hashes.txt rockyou.txt                 # MD5
hashcat -m 1000 hashes.txt rockyou.txt -r best64.rule  # NTLM + mangling rules
hashcat -m 1800 shadow.txt rockyou.txt                 # sha512crypt (/etc/shadow)
hashcat -m 3200 bcrypt.txt rockyou.txt                 # bcrypt — slow, tiny lists only

# John the Ripper
unshadow /etc/passwd /etc/shadow > crack.txt && john crack.txt
zip2john secret.zip > zip.hash && john zip.hash        # crack an encrypted archive
```

## Attack modes (fastest payoff first)
1. **Dictionary** — a wordlist like `rockyou.txt` (14M leaked passwords). Most real cracks come from here.
2. **Rules** — mutate each word (`password → P@ssw0rd!`) with rule sets like `best64`. Huge coverage for little cost.
3. **Mask / brute force** — try character patterns (`?u?l?l?l?d?d`) when you know the shape.
4. **Combinator / hybrid** — join words, append digits.

## Why the KDF choice decides the outcome
The **hash algorithm sets the speed limit**: a GPU does billions of MD5/s but only thousands of **bcrypt**/s. That's the entire defensive point — a slow KDF turns a minutes-long crack into a computationally infeasible one.

> [!tip] Root move
> Fingerprint an unknown hash first (length, charset, format) before choosing the mode — feeding Hashcat the wrong `-m` wastes hours. The **Hashsmith** `identify` command does this for triage.

---
> 🔼 Up: [[Hashing & Passwords]]
