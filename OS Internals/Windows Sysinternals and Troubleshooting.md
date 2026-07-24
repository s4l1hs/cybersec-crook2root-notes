---
title: "Windows Sysinternals and Troubleshooting"
aliases: ["Sysinternals", "Process Explorer", "Procmon", "Autoruns", "TCPView", "PsExec"]
tags:
  - tree/os
  - cyber/foundations/windows
  - type/tool
  - level/operator
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---

# 🪟 Windows Sysinternals and Troubleshooting

> [!abstract] Note of [[Windows]]
> The **Sysinternals Suite** (Mark Russinovich's tools, now Microsoft) is the deepest free window into a live Windows system. Incident responders, malware analysts, and admins all live in these tools — they surface what Task Manager hides.

## The essential toolkit
| Tool | What it does | Security use |
| --- | --- | --- |
| **Process Explorer** (`procexp`) | supercharged Task Manager — full process **tree**, DLLs/handles per process, VirusTotal column, signer verification | spot injected/unsigned processes, find who holds a locked file, verify parentage |
| **Process Monitor** (`procmon`) | real-time **file, registry, process, network** events with stack traces | trace malware behaviour, find DLL-hijack search misses (`NAME NOT FOUND`), config issues |
| **Autoruns** | every **auto-start** location — Run keys, services, tasks, drivers, WMI, logon | hunt persistence; hide-Microsoft + VirusTotal to isolate suspicious entries |
| **TCPView** | live TCP/UDP endpoints with owning process | spot beacons/C2 and unexpected listeners |
| **PsExec** | run processes on remote hosts (SMB + a service) | admin remoting — **and** a classic lateral-movement tool (heavily monitored) |
| **Handle / ListDLLs** | enumerate open handles / loaded DLLs | find LSASS handle abuse, injected modules |
| **Sigcheck** | verify signatures + VirusTotal hash lookup | triage unknown binaries |
| **Sysmon** | persistent event logging (see **Windows Logging and Auditing**) | the telemetry backbone |
| **RAMMap / VMMap** | physical & per-process memory maps | find RWX regions, memory pressure |

## Investigation workflows
**"Is this box compromised?"**
```text
1. Process Explorer → sort by company/signer; flag unsigned + odd parents (e.g. services.exe → cmd)
2. Autoruns → Options: Hide Microsoft entries → scan the rest with VirusTotal
3. TCPView → any process talking to a weird IP/port?
4. Procmon → filter on the suspect PID → watch file/registry/network in real time
5. Strings/Sigcheck the suspect binary; submit hash
```
**"DLL hijack hunt":** run the app under **Procmon**, filter `Result is NAME NOT FOUND` + `Path ends with .dll` — every miss is a writable location where a planted DLL would load.

## Enterprise applicability
- **IR triage** on a suspect endpoint before pulling it off the network.
- **Malware analysis** in a lab (Procmon + Process Explorer + a network sniffer = behavioural analysis without a debugger).
- **Baseline & harden:** Autoruns exports a persistence baseline to diff against later.

> [!warning] Dual-use
> `PsExec` is admin gold *and* attacker gold — its use is a monitored event (service creation **7045**, `PSEXESVC`). Know that running it lights up the SIEM.

> [!tip] Crook → Root
> **Crook** opens Task Manager and gives up. **Root** runs Process Explorer + Procmon + Autoruns and reconstructs exactly what a binary did, where it persists, and who it talks to — the same skill whether you're hunting malware or checking your own implant's footprint.

---
> 🔼 Up: [[Windows]]
