---
title: "Hashsmith CLI"
aliases: ["Hashsmith", "Hashsmith CLI", "Hashsmith Multi-Tool"]
tags:
  - tree/crypto
  - cyber/crypto/applied
  - cyber/tooling
  - type/tool
  - level/operator
Domain:
  - "[[Applied Trust & Tooling]]"
Color: "#FFE119"
---

# 🛠️ Hashsmith CLI

> [!abstract] Leaf of [[Applied Trust & Tooling]]
> **Hashsmith** is the project's open-source, terminal-based cryptography multi-tool, built for the four operations you repeat constantly in security work: **encode, decode, hash, and crack**. It bundles what would otherwise be a dozen separate commands (`base64`, `xxd`, `openssl`, `hashid`, a cracker) behind one consistent interface, distributed through the central package repositories.

> [!warning] Authorized Simulation context
> The cracking features are for **authorized assessment, CTFs, and defensive testing** only — audit hashes you are permitted to.

## Install
```bash
pipx install hashsmith          # PyPI (recommended — isolated CLI install)
pip install hashsmith           # or into a venv
sudo apt install hashsmith      # Debian/Kali repositories
brew install hashsmith          # macOS (Homebrew)
```
```shell-session
$ hashsmith --version
hashsmith 1.4.0
```

## Encode & decode
Handles Base64/Base32, Hex, URL, ROT-N, binary, and more, with an **auto-detect** mode — invaluable in CTF "decode-me" chains:
```shell-session
$ hashsmith encode base64 "Crook2Root"
Q3Jvb2syUm9vdA==

$ hashsmith decode --auto "0x53 0x54 0x45 0x47"          # auto-detects hex
[detected: hex]  STEG

$ hashsmith decode --chain "b64,hex" "NDg2NTZjNmM2Zg=="  # peel layered encodings
[b64 → hex → text]  Hello
```

## Hash & identify
```shell-session
$ hashsmith hash sha256 "password"
5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8

$ hashsmith hash --file report.pdf sha256                # file integrity
sha256(report.pdf) = 3b1f...c9a2

$ hashsmith identify 5f4dcc3b5aa765d61d8327deb882cf99     # fingerprint an unknown hash
[+] Most likely: MD5 (32 hex chars)   also: NTLM, MD4
```

## Crack (triage)
```shell-session
$ hashsmith crack --auto 5f4dcc3b5aa765d61d8327deb882cf99 -w rockyou.txt
[detected: md5]
[+] CRACKED  →  "password"   (0.4s)
```

> [!tip] Where it fits
> Hashsmith is the **triage / CTF** front-end — fast identification and small cracks. For large, GPU-accelerated jobs, hand the hash off to **Hashcat** (see **Password Cracking**). It ties together every branch of this tree: encoding, hashing, and cracking from one shell.

---
> 🔼 Up: [[Applied Trust & Tooling]]
