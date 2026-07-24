---
title: "Windows Security and Access Control"
aliases: ["LSASS", "SAM", "SIDs", "Access Tokens", "UAC", "SePrivileges", "Integrity Levels"]
tags:
  - tree/os
  - cyber/foundations/windows
  - type/technique
  - level/root
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---

# 🪟 Windows Security and Access Control

> [!abstract] Note of [[Windows]]
> Every access decision on Windows comes down to a **token** meeting a **security descriptor**. Master this model and privilege escalation stops being magic — it becomes "which token do I need, and how do I get it?"

> [!warning] Authorized simulation only
> Credential-access and token techniques below are for sanctioned engagements/labs, paired with their detections.

## The players
- **LSA / `lsass.exe`:** the Local Security Authority — validates logons, stores secrets, and holds credential material in memory. The #1 credential-theft target.
- **SAM:** local account database (NTLM hashes).
- **SIDs:** every principal is a **Security Identifier**, e.g. `S-1-5-21-…-500` (the built-in Administrator RID **500**; Domain Admins **512**).

## Access tokens
When you log on, LSA builds an **access token** attached to your processes, containing your SID, group SIDs, **privileges**, and **integrity level**. Access checks compare the token against an object's **DACL** (list of ACEs).
- **Primary token** (the process identity) vs **impersonation token** (a thread acting as another principal) — impersonation is the heart of many escalations.
```cmd
whoami /priv        :: your privileges
whoami /groups      :: group SIDs + integrity level
```

## Privileges (`SeXxxPrivilege`) — the escalation keys
| Privilege | Why it's dangerous |
| --- | --- |
| **SeImpersonatePrivilege** | held by service accounts → **Potato** attacks impersonate SYSTEM |
| **SeDebugPrivilege** | open *any* process → read LSASS memory, inject |
| **SeBackupPrivilege** | read any file (bypass DACL) → copy SAM/SYSTEM |
| **SeTakeOwnership / SeRestore** | seize/replace protected objects |
```text
# Authorized lab: a service account with SeImpersonate → SYSTEM via a "Potato"
PrintSpoofer.exe -i -c cmd        # abuses SeImpersonate to spawn SYSTEM shell
```
Credential material is pulled from LSASS (`mimikatz sekurlsa::logonpasswords`, or `comsvcs.dll MiniDump`) — **detection:** handle opens to `lsass.exe`, protected by **Credential Guard / PPL / RunAsPPL**.

## Integrity levels
Processes run at an **integrity level**: Low → Medium (normal user) → High (elevated admin) → System. A Medium process can't write to a High object even as the same user — this underpins UAC and sandboxing.

## UAC
UAC splits an admin logon into **two tokens**: a filtered Medium token for normal use and a full High token available only after an **elevation** consent. UAC is a convenience boundary, not a security one — **bypasses** abuse auto-elevating binaries and hijacked registry keys (e.g. `fodhelper.exe`). **Defense:** set UAC to "Always Notify", monitor auto-elevation abuse.

> [!tip] Crook → Root
> **Crook** clicks "Run as administrator". **Root** reads `whoami /priv`, spots `SeImpersonate` or `SeDebug`, and turns a service account into SYSTEM — while the defender who understands tokens has already alarmed on the LSASS handle.

---
> 🔼 Up: [[Windows]]
