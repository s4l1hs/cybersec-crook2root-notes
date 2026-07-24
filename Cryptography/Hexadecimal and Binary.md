---
title: "Hexadecimal & Binary"
aliases: ["Hexadecimal", "Hex", "Binary", "Hex and Binary"]
tags:
  - tree/crypto
  - cyber/crypto/encoding
  - type/technique
  - level/crook
Domain:
  - "[[Encoding & Obfuscation]]"
Color: "#FFE119"
---

# 🔢 Hexadecimal & Binary

> [!abstract] Leaf of [[Encoding & Obfuscation]]
> Under every abstraction, data is **bits**. **Hexadecimal** (base-16) is the human-readable shorthand for those bits — two hex digits per byte — and it's everywhere in security: hash digests, memory dumps, packet captures, and shellcode.

## The mental conversions
- **1 byte = 8 bits = 2 hex digits**, range `0x00`–`0xFF` (0–255).
- ASCII maps bytes to characters: `0x41` = `A`, `0x61` = `a`, `0x20` = space, `0x0A` = newline.
- Prefabbed forms you'll read constantly: `\x41` (C/Python escape), `41` (raw hex), `0x41` (literal).

```
Text     H     M     T
ASCII    72    77    84      (decimal)
Hex      48    4D    54
Binary   01001000 01001101 01010100
```

## The workflow
```bash
xxd file.bin                  # hex + ASCII side-by-side dump (the go-to)
echo -n "HMT" | xxd -p        # 484d54  (plain hex)
echo "484d54" | xxd -r -p     # HMT     (reverse)
```
`xxd`/`hexdump` are how you inspect binaries, carve file headers (magic bytes like `PK` for zip, `\x7fELF` for Linux binaries), and read a payload byte by byte.

> [!tip] Root move
> **Endianness** bites everyone: `0x41424344` stored little-endian is the bytes `44 43 42 41`. Exploit development and binary CTFs live or die on getting byte order right.

---
> 🔼 Up: [[Encoding & Obfuscation]]
