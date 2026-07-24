---
title: "macOS Security Mechanisms"
aliases: ["Gatekeeper", "XProtect", "SIP", "TCC", "FileVault", "Notarization"]
tags:
  - tree/os
  - cyber/foundations/macos
  - cyber/defense
  - type/concept
  - level/operator
Domain:
  - "[[macOS]]"
Color: "#FFA500"
---

# 🍎 macOS Security Mechanisms

> [!abstract] Note of [[macOS]]
> Apple stacks many independent defenses — knowing each one, what it stops, and where it *doesn't* apply is essential for both hardening a Mac and understanding real-world macOS malware.

## The layered model
| Mechanism | Protects against | Notes |
| --- | --- | --- |
| **Gatekeeper** | running untrusted downloaded apps | checks code-signing + **notarization** + the **quarantine** flag before first launch |
| **Notarization** | unknown malware in distributed apps | Apple scans and issues a ticket; unnotarized apps are blocked by default |
| **XProtect** | known malware | Apple's built-in signature AV + **XProtect Remediator** (behavioural, runs scans) |
| **SIP** (System Integrity Protection) | tampering with the OS *even as root* | locks `/System`, `/usr` (except `/usr/local`), system processes; toggled only from Recovery (`csrutil`) |
| **TCC** (Transparency, Consent & Control) | apps grabbing private data | per-app consent for camera, mic, files, **Full Disk Access**, screen recording; stored in `TCC.db` |
| **FileVault** | data at rest / theft | full-volume XTS-AES encryption, keyed by the user/Secure Enclave |
| **Hardened Runtime + Library Validation** | injection into signed apps | blocks loading unsigned libs / DYLD injection into protected processes |
| **Sandbox (App Sandbox)** | app doing more than it should | entitlement-scoped file/network/IPC access |

## Quarantine & the "Mark of the Web"
Downloaded files get a `com.apple.quarantine` extended attribute; Gatekeeper reads it on first launch:
```bash
xattr -l ~/Downloads/app.dmg               # see quarantine attr
xattr -d com.apple.quarantine ./tool       # (authorized) clear it
spctl -a -vvv /Applications/Foo.app         # Gatekeeper assessment
csrutil status                              # SIP on/off
```

## How real macOS malware engages this stack
> [!warning] Authorized study
> Modern macOS threats focus on **social-engineering the user** past Gatekeeper/TCC (e.g. tricking them into right-click-Open, or approving a TCC prompt / Full Disk Access) rather than kernel exploits. **TCC bypasses** (abusing an already-permitted app) and **unsigned dylib injection** into non-hardened apps are recurring themes. Defensively: keep SIP **on**, minimise Full Disk Access grants, and monitor `TCC.db`/quarantine changes via the Endpoint Security framework.

## Hardening checklist
- SIP **enabled**; FileVault **on**; Gatekeeper set to App Store / identified developers.
- Audit **Full Disk Access** and Accessibility grants (powerful TCC permissions).
- Prefer notarized software; treat `xattr -d com.apple.quarantine` as a red flag in logs.

> [!tip] Crook → Root
> **Root** maps a macOS objective to the specific control in its way — Gatekeeper (get the user to open it), TCC (get the data-access grant), SIP (don't bother with `/System`) — and the defender hardens each layer and watches the artifacts (quarantine, `TCC.db`, XProtect hits).

---
> 🔼 Up: [[macOS]]
