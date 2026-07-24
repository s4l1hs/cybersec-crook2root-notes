---
title: "macOS Processes and Daemons"
aliases: ["launchd", "launchctl", "LaunchAgents", "LaunchDaemons", "XPC"]
tags:
  - tree/os
  - cyber/foundations/macos
  - type/technique
  - level/operator
Domain:
  - "[[macOS]]"
Color: "#FFA500"
---

# 🍎 macOS Processes and Daemons

> [!abstract] Note of [[macOS]]
> On macOS, **`launchd`** is PID 1 and the single manager of every background job — the equivalent of `init` + `systemd` + `cron` combined. Master `launchd` and you master both service management and the #1 persistence surface.

## launchd & launchctl
`launchd` starts and supervises everything from boot onward, driven by **`.plist`** job definitions. You talk to it with **`launchctl`**:
```bash
launchctl list                              # all loaded jobs (+ PIDs, exit codes)
launchctl print system/com.apple.some.daemon
launchctl load  ~/Library/LaunchAgents/com.x.plist   # (legacy) load a job
launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.x.plist  # modern
```

## Daemons vs Agents (the distinction that matters)
| Type | Runs as | When | Location |
| --- | --- | --- | --- |
| **Daemon** | root / system, **no UI** | at boot, before login | `/Library/LaunchDaemons`, `/System/Library/LaunchDaemons` |
| **Agent** | the **logged-in user**, can touch the GUI | at login | `~/Library/LaunchAgents`, `/Library/LaunchAgents` |
A minimal job `.plist` has a `Label`, `ProgramArguments`, and a trigger (`RunAtLoad`, `StartInterval`, `WatchPaths`, `KeepAlive`).

> [!warning] Persistence (authorized/DFIR)
> A malicious **LaunchAgent/Daemon** is the most common macOS persistence. Hunt them:
> ```bash
> ls -la ~/Library/LaunchAgents /Library/Launch{Agents,Daemons}
> sudo launchctl list | grep -v com.apple      # non-Apple jobs
> ```
> Each is a `.plist` pointing at a `ProgramArguments` binary — verify its signature with `codesign`. Also check **`cron`** (`crontab -l`) and login items (`osascript`/`btm`).

## XPC — the modern IPC
Rather than raw Mach ports, apps talk via **XPC** — a lightweight, sandbox-friendly IPC used to split privileges (a low-priv UI talking to a privileged helper). **XPC services** live inside app bundles (`Contents/XPCServices`); **privileged helper tools** live in `/Library/PrivilegeHelperTools` (installed via `SMJobBless`). Insecure XPC helpers that don't validate the caller are a classic **local privilege-escalation** class on macOS.

## Inspecting live processes
```bash
ps aux | grep -v '\['                       # BSD ps
launchctl list                              # services with exit codes
sudo fs_usage -w -f filesystem <pid>        # live file activity (like strace-lite)
sudo dtrace / Instruments                    # deep tracing
```

> [!tip] Crook → Root
> **Root** treats `launchd` as the map: enumerate non-Apple **LaunchAgents/Daemons** for persistence, verify each job's binary with `codesign`, and scrutinise **XPC** privileged helpers for missing caller validation — the macOS local-privesc sweet spot.

---
> 🔼 Up: [[macOS]]
