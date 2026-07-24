---
title: "macOS CLI and Unix Backend"
aliases: ["macOS CLI", "Zsh", "diskutil", "networksetup", "Homebrew"]
tags:
  - tree/os
  - cyber/foundations/macos
  - type/technique
  - level/apprentice
Domain:
  - "[[macOS]]"
Color: "#FFA500"
---

# 🍎 macOS CLI and Unix Backend

> [!abstract] Note of [[macOS]]
> macOS ships a full BSD userland, so most Linux muscle memory transfers — but the *management* tools (`diskutil`, `networksetup`, `defaults`, `launchctl`) are Apple-specific, and the default shell is now **Zsh**. This note is the operator's map to the macOS command line.

## The shell
Default is **Zsh** (`/bin/zsh`) since Catalina (`bash` 3.2 lingers for licensing). Config: `~/.zshrc`, `~/.zprofile`. Much of GNU/Linux differs — macOS ships **BSD** `ls`, `sed`, `grep` (flags vary; `brew install coreutils gnu-sed` for GNU versions).

## Apple-specific management commands
| Command | Job |
| --- | --- |
| `diskutil list` / `diskutil info disk0` | disks, partitions, APFS containers |
| `diskutil apfs list` | APFS volumes & snapshots |
| `networksetup -listallhardwareports` | network interfaces |
| `networksetup -getdnsservers Wi-Fi` | DNS config |
| `defaults read <domain>` | read app/system preferences (`.plist` values) |
| `scutil --dns` / `scutil --get ComputerName` | system config database |
| `sw_vers` / `system_profiler SPHardwareDataType` | OS version / hardware inventory |
| `pmset -g` | power management |
| `codesign -dv --verbose=4 /path/app` | inspect a binary's signature |
| `spctl --status` | Gatekeeper assessment state |

## Recon reflexes on a macOS host
```bash
sw_vers                                   # ProductVersion, build
whoami; id; dscl . -list /Users           # users (Directory Service, not /etc/passwd)
dscl . -read /Users/$(whoami)             # user record
sudo -l                                   # sudo rights
launchctl list                            # running services/agents
mdfind -name id_rsa                        # Spotlight-powered file search (fast!)
```
> Users/groups come from **Directory Services** (`dscl`), not `/etc/passwd` — `/etc/passwd` exists but is not the source of truth.

## Homebrew — the package manager
Not built in; the de-facto standard. Installs to `/opt/homebrew` (Apple Silicon) or `/usr/local` (Intel):
```bash
brew install nmap                          # formula (CLI tools)
brew install --cask wireshark              # cask (GUI apps)
brew services list                          # brew-managed background services
```
> [!warning] Note for practitioners
> A writable Homebrew prefix owned by the user (`/usr/local` on Intel) has historically been a soft spot — anything on `$PATH` there runs as you, and scripts run by higher-priv contexts can be hijacked.

> [!tip] Crook → Root
> **Root** brings Linux CLI fluency but immediately reaches for `diskutil`, `dscl`, `defaults`, `launchctl`, and `codesign` — the Apple-specific tools that reveal how *this* box is actually configured and secured.

---
> 🔼 Up: [[macOS]]
