---
title: "Windows File System and Registry"
aliases: ["NTFS", "Alternate Data Streams", "ADS", "Windows Registry", "Registry Hives", "SAM"]
tags:
  - tree/os
  - cyber/foundations/windows
  - type/technique
  - level/operator
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---

# 🪟 Windows File System and Registry

> [!abstract] Note of [[Windows]]
> Two places hold the truth on a Windows host: **NTFS** (where files and their hidden metadata live) and the **Registry** (the central config/state database). Both are goldmines for attackers and the primary hunting ground for defenders and forensics.

## NTFS internals
- **$MFT (Master File Table):** every file/dir is a record in this table. Small files live *inside* their MFT record (resident data). Forensic timelines are rebuilt from MFT `$STANDARD_INFORMATION` vs `$FILE_NAME` timestamps — and **timestomping** manipulates exactly these.
- **Metafiles:** `$LogFile` (journaling), `$UsnJrnl` (USN change journal — records every file change; anti-forensics tries to clear it), `$Bitmap`, `$Secure` (ACL store).
- **Permissions:** NTFS ACLs (ACEs) are separate from share permissions; `icacls file` reads/sets them.

### Alternate Data Streams (ADS)
NTFS files can carry hidden extra streams `file:stream` — invisible to `dir` and Explorer:
```cmd
echo malicious > report.txt:hidden.txt        :: write a hidden stream
more < report.txt:hidden.txt                  :: read it
dir /r                                         :: reveal streams
Get-Item report.txt -Stream *                  :: PowerShell
```
The **`Zone.Identifier`** ADS is the "Mark of the Web" (MotW) that flags internet-downloaded files — attackers strip it to dodge SmartScreen.

## The Registry
A hierarchical database of keys/values under root hives:
| Root | Holds |
| --- | --- |
| `HKLM` | machine-wide config (the important one) |
| `HKCU` | current user's settings (`NTUSER.DAT`) |
| `HKCR` / `HKU` / `HKCC` | file associations / all users / current hardware |

### The hives that matter for credentials
On disk under `C:\Windows\System32\config\`:
| Hive | Contains |
| --- | --- |
| **SAM** | local user **password hashes** (NTLM) |
| **SECURITY** | LSA secrets, cached domain creds, service account passwords |
| **SYSTEM** | the **boot key** needed to decrypt SAM, plus services & config |
| **SOFTWARE** | installed software, autoruns |

> [!warning] Authorized simulation
> With SYSTEM access, dumping `SAM`+`SYSTEM` yields crackable local hashes (offline attack — see the Cryptography tree's cracking notes). Detection: access to these hives by non-system processes.
```cmd
reg save HKLM\SAM sam.hiv & reg save HKLM\SYSTEM sys.hiv     :: then secretsdump offline
```

### Persistence & recon in the Registry
```cmd
reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Run"       :: autoruns
reg query HKLM\SYSTEM\CurrentControlSet\Services                     :: services
```
Run/RunOnce keys, service `ImagePath`s, and Winlogon `Userinit`/`Shell` are classic **persistence** footholds — and exactly what **Autoruns** (see **Windows Sysinternals and Troubleshooting**) enumerates.

> [!tip] Crook → Root
> **Crook** browses `C:\`. **Root** reads the **$MFT** for the real timeline, hides tools in an **ADS**, and knows the three hives (**SAM/SYSTEM/SECURITY**) that turn one foothold into every credential on the box.

---
> 🔼 Up: [[Windows]]
