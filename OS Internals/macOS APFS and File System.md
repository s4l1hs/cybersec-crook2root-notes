---
title: "macOS APFS and File System"
aliases: ["APFS", "macOS File System", "plist", "App Bundles", "Library hierarchy"]
tags:
  - tree/os
  - cyber/foundations/macos
  - type/concept
  - level/apprentice
Domain:
  - "[[macOS]]"
Color: "#FFA500"
---

# 🍎 macOS APFS and File System

> [!abstract] Note of [[macOS]]
> macOS layout looks Unix on the surface but is organised around **APFS**, a **sealed** system volume, layered **`/Library`** domains, and application **bundles** — plus `.plist` config everywhere. Knowing where things live is the whole game for DFIR and persistence hunting.

## APFS
The Apple File System (default since High Sierra) is copy-on-write with modern features:
- **Containers & volumes:** one APFS *container* holds multiple *volumes* sharing space (System, Data, VM, Preboot, Recovery).
- **Snapshots:** near-instant point-in-time copies — Time Machine local snapshots; forensically valuable (and an anti-rollback control).
- **Clones:** copy-on-write file copies (instant, space-efficient).
- **Native encryption** (per-file), the backend for **FileVault**.
```bash
diskutil apfs list
tmutil listlocalsnapshots /
```

## The signed system volume (SSV)
Since Big Sur, the **System volume is read-only and cryptographically sealed** (a Merkle tree of hashes). You cannot modify system files even as root — you'd break the seal. User data lives on a separate **Data** volume, joined by **firmlinks** so the tree looks unified. This is why classic "drop a binary in /usr/bin" persistence no longer works.

## The `/Library` domains (where config & persistence live)
| Path | Scope |
| --- | --- |
| `/System/Library` | Apple only (sealed) — don't touch |
| `/Library` | administrator, **all users** |
| `~/Library` | the current user |
Key subfolders (persistence & forensics): **`LaunchDaemons`**, **`LaunchAgents`** (auto-run — see **macOS Processes and Daemons**), `Application Support`, `Preferences` (`.plist`), `Logs`, `Keychains`.

## Application bundles
A `.app` is a **directory**, not a file: `MyApp.app/Contents/{MacOS/<binary>, Info.plist, Resources, _CodeSignature}`. Inspect from the CLI:
```bash
ls -R /Applications/Safari.app/Contents/MacOS
codesign -dv --verbose=4 /Applications/Safari.app
```

## Property lists (`.plist`)
The universal config format (XML or binary). Convert & read:
```bash
plutil -p ~/Library/Preferences/com.apple.dock.plist    # pretty-print
defaults read com.apple.dock                             # same via defaults
plutil -convert xml1 -o - some.plist                     # binary → XML
```
LaunchDaemon/Agent definitions, app preferences, and MDM profiles are all `.plist` — the first place to look for persistence and configuration.

> [!tip] Crook → Root
> **Root** knows the System volume is sealed (persistence moved to **LaunchAgents/Daemons** in `/Library` and `~/Library`), reads config from **`.plist`** with `plutil`/`defaults`, and treats every `.app` as a directory to inspect and verify with `codesign`.

---
> 🔼 Up: [[macOS]]
