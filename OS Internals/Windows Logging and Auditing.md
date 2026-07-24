---
title: "Windows Logging and Auditing"
aliases: ["Windows Event Logs", "Event IDs", "ETW", "Sysmon", "Windows Auditing"]
tags:
  - tree/os
  - cyber/foundations/windows
  - cyber/defense
  - type/technique
  - level/operator
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---

# 🪟 Windows Logging and Auditing

> [!abstract] Note of [[Windows]]
> Windows records almost everything — if auditing is configured. This note is the defender's core telemetry map (and the attacker's guide to what they leave behind).

## The Event Log
Logs live under `%SystemRoot%\System32\winevt\Logs\*.evtx`, grouped into channels: **Security**, **System**, **Application**, and **PowerShell/Sysmon** operational logs.
```powershell
Get-WinEvent -LogName Security -MaxEvents 20
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 50   # failed logons
wevtutil qe Security /c:5 /f:text
```

## The Event IDs that matter
| ID | Meaning |
| --- | --- |
| **4624 / 4625** | successful / failed logon (watch **Type 3** network, **Type 10** RDP) |
| **4672** | special privileges assigned (admin logon) |
| **4688** | process creation (enable **command-line auditing**!) |
| **4720 / 4732** | user created / added to a privileged group |
| **4768 / 4769 / 4771** | Kerberos TGT / TGS / pre-auth failed (roasting) |
| **7045** | new service installed (persistence) |
| **4698** | scheduled task created |
| **1102** | **audit log cleared** — one of the highest-fidelity alerts in the SOC |
| **4104** | PowerShell **script-block logging** (deobfuscated script text) |

## ETW — the firehose under everything
**Event Tracing for Windows** is the low-level telemetry bus that most EDR consumes (providers like `Microsoft-Windows-Threat-Intelligence`, DNS, WMI). Attackers attempt **ETW patching/blinding** (patching `EtwEventWrite` in their own process) — defenders detect the resulting telemetry gaps and protect key providers.

## Sysmon — the essential add-on
System Monitor (Sysinternals) writes rich events to `Microsoft-Windows-Sysmon/Operational`:
| Sysmon ID | Captures |
| --- | --- |
| **1** | process creation + **full command line + hashes + parent** |
| **3** | network connection (with process) |
| **7** | image/DLL load (catch unsigned/suspicious DLLs) |
| **8 / 10** | CreateRemoteThread / process access (injection, LSASS reads) |
| **11 / 13** | file create / registry set (drops & persistence) |
| **22** | DNS query |
Deploy with a curated config (e.g. SwiftOnSecurity/Olaf's) and forward to a SIEM.

## The golden rules
- **Command-line + script-block logging on**, or 4688/4104 are near-useless.
- **Forward logs off-host in real time** (WEF/agent) — a cleared local log (**1102**) can't erase what already left the box. This neutralises most anti-forensics before it starts.
- Alert on the **absence** of expected logs (a silenced source is itself an IOC).

> [!tip] Blue-team takeaway
> Every offensive move in the Windows tree — LSASS reads, new services, Kerberoasting, log clearing — maps to an Event ID above. Detection engineering is turning this table into alerts (see the Defensive Security domain).

---
> 🔼 Up: [[Windows]]
