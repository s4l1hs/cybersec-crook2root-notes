---
title: "1.1 Networking Masterclass"
aliases: ["Networking Masterclass", "Networking", "OSI Model", "TCP-IP Model", "IP Addreses & Subnets", "Ports 101", "DNS", "DHCP", "NAT", "Domain", "Routing Protocols", "Core Protocols", "Secure Protocols", "HTTP", "VPN", "Nmap", "Tcpdump", "Intro to LAN"]
tags:
  - tree/networking
  - cyber/networking/osi
  - cyber/networking/dns
  - cyber/networking/dhcp
  - cyber/networking/nat
  - cyber/networking/routing
  - cyber/networking/protocols
  - cyber/networking/scanning
  - type/concept
  - level/crook
Domain:
  - "[[Networking]]"
Color: "#42D4F4"
---

# 🌐 1.1 Networking Masterclass

> [!abstract] The Masterclass
> This is the complete networking chapter of the Crook → Root journey — from *"what is an IP address?"* to hand-crafting scans and reasoning about attacks layer by layer. Read it top to bottom as a textbook, or jump to a section. Everything else in the vault assumes the fluency you build here. **`#level/crook` → `#level/apprentice`**

> [!tip] Chapter Map
> **Foundations** → **** · **** · **** · ****
> **Addressing** → **** · **** · ****
> **Services** → **** · **** · **** · **** · ****
> **The Web & Security** → **** · **** · **** · ****
> **Tooling** → **** · ****

---

## How a Network Works

A **network** is simply two or more devices exchanging data. Networks are classified by scope:

| Term | Scope | Example |
| --- | --- | --- |
| **LAN** | Single building/site | Office or home network |
| **MAN** | A city | A university's multi-campus link |
| **WAN** | Countries/continents | The Internet itself |

Devices on the same LAN trust each other far more than they trust the outside world — which is exactly why **a single foothold inside a LAN is so valuable to an attacker**. The rest of this chapter is the machinery that makes that communication happen, and every piece of it is something you will one day attack or defend.

### Ping & ICMP — "Are you there?"
The first question in any network interaction is *reachability*. **Ping** uses **ICMP (Internet Control Message Protocol)** echo request/reply packets to test it:
```bash
ping -c 4 10.10.10.5      # 4 probes (Linux/macOS)
ping -t 10.10.10.5         # continuous (Windows)
```
Read the **TTL** (Time To Live — decreases by 1 per router hop; ~64 ≈ Linux, ~128 ≈ Windows, ~255 ≈ network gear) and **round-trip time**. A **ping sweep** (`nmap -sn`, `fping`) is the very first step of recon: *"which hosts are even alive?"* Many organisations block inbound ICMP at the perimeter specifically to blunt this.

---

## The OSI Model

The **OSI (Open Systems Interconnection) Model** is a 7-layer reference framework describing how data travels from an app on one device to an app on another. Nobody ships an exact OSI stack — the real world runs on **TCP/IP** — but OSI survives as **the** troubleshooting and security vocabulary: *"Is this a Layer 1 cable problem, a Layer 3 routing problem, or a Layer 7 application bug?"* That question is called **layer isolation**.

| # | Layer | PDU | Core Job | Examples |
| --- | --- | --- | --- | --- |
| 7 | **Application** | Data | User-facing services | HTTP, DNS, SSH, SMTP |
| 6 | **Presentation** | Data | Translation, encryption, compression | TLS/SSL, JPEG, UTF-8 |
| 5 | **Session** | Data | Open/manage/close sessions | RPC, NetBIOS, sockets |
| 4 | **Transport** | Segment/Datagram | End-to-end delivery, ports | TCP, UDP |
| 3 | **Network** | Packet | Logical addressing & routing | IP, ICMP · routers |
| 2 | **Data Link** | Frame | Physical (MAC) addressing | Ethernet, ARP · switches |
| 1 | **Physical** | Bits | Raw signal transmission | Cabling, Wi-Fi · hubs |

> **Mnemonic (bottom-up):** "**P**lease **D**o **N**ot **T**hrow **S**ausage **P**izza **A**way".

### Encapsulation — how data actually travels
Each layer wraps the layer above in its own header (nested envelopes); the receiver strips them in reverse. This is why a packet capture shows an Ethernet **frame** wrapping an IP **packet** wrapping a TCP **segment** wrapping your HTTP request.

```mermaid
flowchart TB
    A7["L7 Data"] --> A4["L4 Segment (+Port)"] --> A3["L3 Packet (+IP)"] --> A2["L2 Frame (+MAC)"] --> A1["L1 Bits"]
    A1 -->|"cable / Wi-Fi"| B1["L1 Bits"]
    B1 --> B2["L2 Frame"] --> B3["L3 Packet"] --> B4["L4 Segment"] --> B7["L7 Data"]
```

### Security cheat-sheet by layer
| Layer | Example Attack | Primary Defense |
| --- | --- | --- |
| 1 Physical | Wiretapping, rogue devices | Locked racks, port security |
| 2 Data Link | ARP spoofing, MAC flooding, VLAN hopping | Dynamic ARP Inspection, port security |
| 3 Network | IP spoofing, ICMP tunneling | ACLs, ICMP filtering |
| 4 Transport | SYN flood, port scanning | SYN cookies, stateful **filtering** |
| 6/7 Present./App. | TLS downgrade, injection, XSS | TLS enforcement, WAF, input validation |

Most modern breaches happen at **Layer 7** — closest to the user, and the user is the easiest thing to fool.

---

## The TCP-IP Model

The **TCP/IP Model** (a.k.a. DoD Model, RFC 1122) is the practical 4-layer model every real device implements. It collapses OSI's top three layers into one **Application** layer — because in practice a program handles its own sessions and encoding.

| TCP/IP Layer | OSI Equivalent | Protocols |
| --- | --- | --- |
| **Application** | L7 + L6 + L5 | HTTP, DNS, SSH, SMTP, FTP |
| **Transport** | L4 | TCP, UDP |
| **Internet** | L3 | IP, ICMP, IPSec |
| **Link** | L2 + L1 | Ethernet, Wi-Fi, ARP |

Almost every offensive tool is organised around these layers: ****Nmap**** reads TCP flags at the Transport layer; `hping3`/Scapy hand-craft packets at the Internet/Transport layers; ****tcpdump**** decodes all four at once. Web attacks live entirely at the **Application** layer — the lower layers already did their job correctly by the time the payload arrives.

---

## Local Area Networks

How devices on a LAN are wired is its **topology**:
- **Star** (dominant today) — every device to a central switch; one failure drops only that device.
- **Bus** — all devices share one backbone cable; one break kills the segment (legacy).
- **Ring** — each device connects to two neighbours in a loop (token passing; legacy).
- **Mesh/Hybrid** — real networks are a star-of-stars uplinked to a core router.

```mermaid
graph TD
    SW["Switch (Star)"] --- A["Host A"] & B["Host B"] & C["Host C"] & D["Host D"]
```

A **flat LAN** (one broadcast domain, no VLANs) is an attacker's dream: **ARP spoofing** (every host trusts every ARP reply → full MITM with `ettercap`), **VLAN hopping**, and **rogue **DHCP**** all become trivial. Defensive controls: **VLAN segmentation** with inter-VLAN firewall ACLs, **Dynamic ARP Inspection**, **DHCP snooping**, and **802.1X**. Map a LAN yourself:
```bash
ip addr show ; ip route ; arp -a
nmap -sn 192.168.1.0/24          # ping-sweep the subnet
netdiscover -r 192.168.1.0/24     # ARP-based discovery
```

---

## IP Addressing and Subnetting

An **IPv4 address** is 32 bits, written as four dotted-decimal **octets** (`0–255`). Every subnetting calculation is just binary arithmetic on those 32 bits.

![[Pasted image 20251017110201.png]]

**Private ranges (RFC 1918)** — not routable on the public Internet; they reach it via **NAT**:

| Range | CIDR | Use |
| --- | --- | --- |
| `10.0.0.0 – 10.255.255.255` | `/8` | Large enterprise |
| `172.16.0.0 – 172.31.255.255` | `/12` | Medium (Docker defaults) |
| `192.168.0.0 – 192.168.255.255` | `/16` | Home/small office |
| `127.0.0.0/8` | — | Loopback (`localhost`) |
| `169.254.0.0/16` | — | APIPA (DHCP failed) |

### Subnet masks & CIDR
A **subnet mask** splits an address into **network** and **host** portions. `/24` = `255.255.255.0` = first 24 bits network, last 8 host.

| CIDR | Mask | Total | Usable Hosts |
| --- | --- | --- | --- |
| `/24` | `255.255.255.0` | 256 | 254 |
| `/25` | `255.255.255.128` | 128 | 126 |
| `/26` | `255.255.255.192` | 64 | 62 |
| `/27` | `255.255.255.224` | 32 | 30 |
| `/28` | `255.255.255.240` | 16 | 14 |
| `/30` | `255.255.255.252` | 4 | 2 (point-to-point) |

**Usable hosts = 2^(host bits) − 2** (minus network + broadcast). **Worked example** — split `192.168.1.0/24` into four `/26`s (magic number `256 − 192 = 64`):

| Subnet | Network | Usable | Broadcast |
| --- | --- | --- | --- |
| 1 | `192.168.1.0/26` | `.1–.62` | `.63` |
| 2 | `192.168.1.64/26` | `.65–.126` | `.127` |
| 3 | `192.168.1.128/26` | `.129–.190` | `.191` |
| 4 | `192.168.1.192/26` | `.193–.254` | `.255` |

```bash
ipcalc 192.168.1.0/26      # instant network/broadcast/range
nmap -sn 10.10.10.0/28      # scan exactly the block you computed
```
**Attacker relevance:** CIDR fluency means scanning `10.10.10.0/24` once instead of guessing 254 hosts; a foothold's subnet mask reveals which neighbours are reachable for **lateral movement**. **Defenders:** segment aggressively — an oversized flat `/16` multiplies blast radius.

---

## Ports and Services

A **port** is a 16-bit number (0–65535) letting one IP run many services at once. A full connection (**socket**) is a 5-tuple: *protocol, src IP, src port, dst IP, dst port*.

| Range | Name | Meaning |
| --- | --- | --- |
| 0–1023 | Well-Known | Standard services; binding needs root |
| 1024–49151 | Registered | Vendor apps (e.g. 3306 MySQL) |
| 49152–65535 | Ephemeral | Temporary source ports for outbound conns |

**Ports you must know:**

| Port | Svc | | Port | Svc |
| --- | --- | --- | --- | --- |
| 21 | FTP | | 143 | IMAP |
| 22 | SSH | | 389 | LDAP (**AD**) |
| 23 | Telnet | | 443 | HTTPS |
| 25 | SMTP | | 445 | SMB |
| 53 | DNS | | 3306 | MySQL |
| 67/68 | DHCP | | 3389 | RDP |
| 80 | HTTP | | 5432 | PostgreSQL |
| 110 | POP3 | | 161 | SNMP |

Nmap reports each port as **open** (listening), **closed** (RST returned), or **filtered** (a firewall drops the probe). Every open port is a potential entry point — the defensive principle is **minimise exposed surface**.

---

## Core Protocols (TCP, UDP, ICMP)

**TCP** is connection-oriented and reliable; every connection opens with the **three-way handshake**:
```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    C->>S: SYN
    S-->>C: SYN-ACK
    C->>S: ACK
    Note over C,S: Established → ordered, guaranteed data
```
**UDP** is connectionless and best-effort — fast, no handshake (DNS, VoIP, streaming). **ICMP** is control/diagnostics (`ping`, `traceroute`).

| | TCP | UDP |
| --- | --- | --- |
| Connection | Handshake | None |
| Reliability | Guaranteed, ordered | Best-effort |
| Use | Web, SSH, email | DNS, streaming, VoIP |

**Security:** `-sS`/`-sT` scans probe TCP, `-sU` probes UDP; **SYN floods** abuse the handshake; **UDP amplification** (DNS/NTP) powers DDoS.

---

## DNS (Domain Name System)

**DNS** resolves names → IPs. Application layer, **UDP/TCP port 53**.

| Record | Purpose |
| --- | --- |
| **A / AAAA** | Hostname → IPv4 / IPv6 |
| **CNAME** | Alias one domain to another |
| **MX** | Mail server for a domain |
| **TXT** | SPF/DKIM/DMARC, verification |
| **NS / PTR** | Delegation / reverse lookup |

```mermaid
sequenceDiagram
    participant C as Client
    participant R as Resolver
    participant Root
    participant TLD as .com
    participant Auth as example.com
    C->>R: A? example.com
    R->>Root: A? example.com
    Root-->>R: ask .com
    R->>TLD: A? example.com
    TLD-->>R: ask example.com NS
    R->>Auth: A? example.com
    Auth-->>R: 172.17.2.172
    R-->>C: 172.17.2.172 (cached, TTL)
```
```bash
dig example.com A +short ; dig example.com MX ; dig -x 172.17.2.172
dig axfr @ns1.example.com example.com   # zone transfer if misconfigured
```
**Attacks:** cache poisoning/spoofing (→ **phishing**), **zone transfer** (full record dump → **Subdomain Enumeration**), **DNS tunneling** (C2/exfil past firewalls), **subdomain takeover**. **Defense:** DNSSEC, disable AXFR, monitor long TXT queries.

---

## DHCP

**DHCP** auto-configures new hosts (IP, gateway, DNS). Server on **UDP 67**, client **UDP 68**. The exchange is **DORA**:
```mermaid
sequenceDiagram
    participant C as Client
    participant S as DHCP Server
    C->>S: DHCPDISCOVER (broadcast)
    S-->>C: DHCPOFFER
    C->>S: DHCPREQUEST
    S-->>C: DHCPACK (lease granted)
```
**Attacks:** **rogue DHCP** (answer faster than the real server → hand victims a malicious gateway/DNS → MITM); **DHCP starvation** (exhaust the pool → DoS). **Defense:** **DHCP snooping** (trust only authorised ports) + Dynamic ARP Inspection.

---

## Domain Names and Hierarchy

Domain names are hierarchical, read right-to-left: **root → TLD → second-level → subdomain**.

```mermaid
flowchart TD
    Root["'.' root"] --> COM[".com TLD"]
    COM --> the lab["the lab (SLD)"]
    the lab --> WWW["www"] & ADMIN["admin (subdomain)"]
```
- **TLD** — `.com` (gTLD) or `.co.uk` (ccTLD).
- **Second-level** — `the lab`; 63-char limit, `a–z 0–9 -`.
- **Subdomain** — `admin.example.com`; nestable to 253 chars total.

```bash
whois example.com ; dig example.com NS +short
```
Forgotten subdomains (`dev.`, `staging.`, `vpn.`) are recon gold — see **Subdomain Enumeration** and **OSINT**.

---

## NAT (Network Address Translation)

**NAT** lets many private IPs share one public IP — the reason IPv4 didn't run out. The router rewrites source IP/port on the way out and keeps a **translation table** to reverse it on replies.

![[5f04259cf9bf5b57aed2c476-1719849362861.svg]]

```mermaid
sequenceDiagram
    participant L as Laptop 192.168.0.129:15401
    participant R as NAT Router 212.3.4.5
    participant S as Server:443
    L->>R: src 192.168.0.129:15401
    Note over R: maps ⇄ 212.3.4.5:19273
    R->>S: src 212.3.4.5:19273
    S-->>R: reply → :19273
    R-->>L: reply → :15401
```
**Types:** Static (1:1), Dynamic (pool), **PAT/overload** (many→one by port — home routers), **Port forwarding/DNAT** (expose an internal service). **Attacker relevance:** **pivoting** — a NAT'd foothold relays into internal `192.168.x.x` hosts an external attacker can't reach (`ssh -L/-R`, `chisel`, `ligolo`). NAT is **not a firewall** — you still need explicit rules.

---

## Routing

**Routing** moves packets across networks. Routes are **static** (hand-configured) or **dynamic** (learned via protocols):

| Protocol | Type | Idea |
| --- | --- | --- |
| **RIP** | Interior, distance-vector | Fewest hops (small nets) |
| **OSPF** | Interior, link-state | Full map, shortest path (scales) |
| **EIGRP** | Interior, hybrid (Cisco) | Bandwidth/delay cost |
| **BGP** | Exterior | The Internet's backbone between ASes |

```bash
ip route show ; traceroute example.com ; mtr example.com
```
**Attacks:** **route injection** (unauthenticated OSPF/RIP → MITM), **BGP hijacking** (announce prefixes you don't own — has rerouted whole services). **Defense:** authenticate updates (MD5/keychains), **RPKI** for BGP.

---

## Secure Protocols and Encryption

Most classic protocols were plaintext — anyone on the path reads them with **tcpdump**. **Secure protocols** wrap them in **TLS** or **SSH**. Know the pairs:

| Cleartext | Secure | Port |
| --- | --- | --- |
| HTTP (80) | HTTPS | 443 |
| FTP (21) | FTPS / SFTP | 990 / 22 |
| Telnet (23) | SSH | 22 |
| SMTP/POP3/IMAP | +TLS (SMTPS/POP3S/IMAPS) | 465·995·993 |
| DNS (53) | DoT / DoH | 853 / 443 |

### The TLS handshake
Provides **confidentiality, integrity, and authentication** (certificates):
```mermaid
sequenceDiagram
    participant C as Client
    participant S as Server
    C->>S: ClientHello (ciphers)
    S-->>C: ServerHello + Certificate
    Note over C,S: verify cert vs trusted CA
    C->>S: key exchange (ECDHE)
    C->>S: encrypted data 🔒
```
### SSH
The secure replacement for Telnet, and the workhorse of remote admin and pivoting:
```bash
ssh -i key.pem user@host ; scp file user@host:/tmp/
ssh -L 8080:internal:80 user@jump    # tunnel/pivot
```
**Attacks:** downgrade (`sslstrip`, mitigated by **HSTS**), weak TLS (`testssl.sh`). SSH is brute-forced early (**Hydra**) — use keys, disable root, `fail2ban`.

---

## HTTP and the Web

**HTTP** is the stateless request/response protocol of the web. A **URL** encodes how to reach a resource:

![[34ad66d8b90aaaa35f9536d3b152ea97.png]]

`scheme://user:pass@host:port/path?query#fragment`

**Methods:** GET (read), POST (create), PUT (update), DELETE, PATCH (partial). **Status codes:** `2xx` success, `3xx` redirect, `4xx` client error (401 auth, 403 forbidden, 404 missing), `5xx` server error.
```bash
curl -v https://example.com          # request + response headers
curl -X POST -d "u=a&p=b" .../login
```
Because HTTP is stateless, apps fake "logged in" with **cookies or tokens**. If a page reflects unvalidated input into HTML, injected markup (`<h1>`, `<a>`) is **HTML injection**; injected script (`<script>`) is ****XSS****.

---

## VPNs and Tunneling

A **VPN** builds an encrypted **tunnel** across the Internet so remote networks act as one private network.

```mermaid
flowchart LR
    A["Office A LAN"] --> GA["Gateway A"] -->|"🔒 tunnel"| GB["Gateway B"] --> B["Office B LAN"]
```
**Technologies:** PPTP (legacy, weak), **IPSec** (strong, ubiquitous), **OpenVPN** (TLS-based, flexible), **WireGuard** (modern best-practice). **Attacker relevance:** post-foothold **pivoting** (`sshuttle`, `ligolo-ng`, WireGuard) routes your toolkit into the internal subnet; VPN portals are prime **phishing** targets. **Defense:** enforce ****MFA**** on every VPN login; retire PPTP.

---

## Web Security Primer

The web is the biggest attack surface, and it all rides on HTTP. Vulnerabilities live at **trust boundaries** (user→server, server→DB) where input isn't validated or output isn't encoded.

**Browser guardrails:** Same-Origin Policy (isolation), CORS (controlled relaxation), cookie flags (`HttpOnly`/`Secure`/`SameSite`), CSP (XSS mitigation), HSTS (forces HTTPS). **Vulnerability classes** (Phase 2 deep-dives): injection (**3. Injection**, **Command Injection**, **SQLi**), **XSS**, **broken access control**, **SSRF**. The full catalogue is the **OWASP Top 10**.

---

## Nmap (Network Mapper)

**Nmap** discovers live hosts, open ports, services, versions, and OS — the backbone of enumeration. A SYN scan probes the handshake:
```mermaid
sequenceDiagram
    participant N as Nmap
    participant T as Target port
    N->>T: SYN
    alt OPEN
        T-->>N: SYN/ACK (open)
    else CLOSED
        T-->>N: RST
    else FILTERED
        Note over T: no reply (firewall)
    end
```

| Option | Meaning | | Option | Meaning |
| --- | --- | --- | --- | --- |
| `-sT` | TCP connect | | `-sV` | service/version |
| `-sS` | SYN stealth | | `-O` | OS detection |
| `-sU` | UDP | | `-A` | aggressive (all) |
| `-sn` | ping sweep | | `-Pn` | skip host discovery |
| `-p-` | all 65535 ports | | `-T<0-5>` | timing |
| `-F` | top 100 ports | | `-oA` | save all formats |

```bash
nmap -sn 10.10.10.0/24                 # who's alive
nmap -sS -sV -O -T4 10.10.10.5        # stealth + versions + OS
nmap -p- -T4 10.10.10.5                # all ports
nmap -sC -sV -oA scan 10.10.10.5      # default scripts + save
nmap --script vuln 10.10.10.5          # NSE vuln scripts
```
Deep-dive methodology: **Nmap Port Scans** · **Nmap Live Host Discovery**.

---

## tcpdump (Packet Capture)

**tcpdump** captures/inspects packets from the CLI — perfect over SSH. It shares **BPF** filter syntax with Wireshark.

| Option | Meaning | | Option | Meaning |
| --- | --- | --- | --- | --- |
| `-i IF` | interface (`-i any`) | | `-A` | ASCII payloads (cleartext creds) |
| `-w / -r` | write / read `.pcap` | | `-X` | hex + ASCII |
| `-c N` | N packets | | `-e` | include MAC |
| `-nn` | no name/port resolution | | `host/port/src/dst` | BPF filters |

```bash
sudo tcpdump -i any -nn udp port 53      # hunt DNS tunneling/exfil
sudo tcpdump -i any -A 'tcp port 80'     # cleartext HTTP payloads
sudo tcpdump -i eth0 -w cap.pcap 'host 10.10.10.5'
tcpdump "tcp[tcpflags] == tcp-syn"        # SYN-only (find scans)
```
Plaintext protocols (HTTP, FTP, Telnet) leak credentials visible with `-A` — the whole reason **secure protocols** exist. Save a `.pcap` and open it in **Wireshark** for deep analysis.

---

## 🔗 Related Master Notes & Deep-Dives
- **1.2 Linux and Command Line** — the terminal you run all this from
- **1.6 Defensive Groundwork** — firewalls, sessions, MFA
- **1.4 Docker and Containers** — networking inside containers
- Phase 2 → **Nmap Port Scans** · **Nmap Live Host Discovery** · **Reconnaissance** · **OWASP Top 10** · **Subdomain Enumeration**
- [[Networking]] — domain hub
