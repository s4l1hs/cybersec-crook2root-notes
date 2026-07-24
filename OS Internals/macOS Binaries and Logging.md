---
title: "macOS Binaries and Logging"
aliases: ["Mach-O", "Universal Binary", "Unified Logging", "macOS Logs", "dylib"]
tags:
  - tree/os
  - cyber/foundations/macos
  - type/technique
  - level/operator
Domain:
  - "[[macOS]]"
Color: "#FFA500"
---

# 🍎 macOS Binaries and Logging

> [!abstract] Note of [[macOS]]
> Two practical skills round out macOS mastery: reading its executable format (**Mach-O**) and its telemetry (**Unified Logging**). One is how you analyse a binary; the other is how you investigate what happened.

## Mach-O — the executable format
macOS/iOS use **Mach-O** (not ELF/PE). Structure: a **header** → **load commands** (segments, linked dylibs, entry point, code-signature) → segments (`__TEXT` code, `__DATA`).
```bash
file /bin/ls
otool -hv /bin/ls                 # Mach-O header
otool -L /bin/ls                  # linked dynamic libraries (dylibs)
otool -tv /bin/ls | head          # disassembly
nm /bin/ls                        # symbols
codesign -dv --verbose=4 /bin/ls  # signature, team ID, entitlements
```
- **dylibs** (`.dylib`) are the shared libraries; the **dyld** loader resolves them. Historical injection vectors: **`DYLD_INSERT_LIBRARIES`** (blocked for hardened/SIP-protected binaries) and **dylib hijacking** (a binary loading a dylib from a writable/missing path).
- **Entitlements** (embedded in the signature) grant capabilities — inspect them to understand what an app is *allowed* to do.

## Universal (fat) binaries
A **Universal Binary** packs multiple architectures (x86_64 + **arm64**) in one file so it runs native on Intel and Apple Silicon:
```bash
file /bin/ls                       # "Mach-O universal binary with 2 architectures"
lipo -info /bin/ls                 # list slices
lipo /bin/ls -thin arm64 -output ls_arm64   # extract one arch
```
On Apple Silicon, Intel slices run under the **Rosetta 2** translator.

## Unified Logging (the modern log system)
Since macOS Sierra, most logs are **not** flat files — they're a structured, in-memory + on-disk store queried with the `log` tool. Traditional files remain for a few things (`/var/log/`, `/var/audit/` for BSM/OpenBSM auditing).
```bash
log stream --predicate 'process == "sshd"' --info      # live tail
log show --last 1h --predicate 'eventMessage CONTAINS "Failed"'   # historical
log show --predicate 'subsystem == "com.apple.TCC"'    # TCC decisions
sudo praudit /var/audit/*                               # BSM audit trail
```
> [!warning] DFIR note
> Because Unified Logs are structured and partly in-memory with retention limits, **collect them early**. Key forensic sources: Unified Logs, `~/Library` (LaunchAgents, quarantine xattrs), `TCC.db`, `KnowledgeC.db`/`spotlight`, and FSEvents.

## Endpoint Security framework
Modern macOS EDR subscribes to the **Endpoint Security (ES)** framework for real-time `exec`, `open`, `fork`, and mount events from user space — the macOS analogue of Sysmon/auditd.

> [!tip] Crook → Root
> **Root** reads a Mach-O with `otool`/`codesign` to judge what a binary links and is entitled to, remembers Universal binaries carry both arches, and pulls **Unified Logs** (`log show`) + the ES framework — not flat files — to reconstruct events.

---
> 🔼 Up: [[macOS]]
