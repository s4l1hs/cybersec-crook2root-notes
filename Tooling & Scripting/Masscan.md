---
title: "Masscan"
aliases: ["Mass IP Port Scanner"]
tags: [tree/tooling, cyber/tooling/offensive/masscan, level/master]
Domain: "[[Network Discovery Tools]]"
Color: "#708090"
---

# Masscan

Masscan is an asynchronous, stateless Internet-scale SYN scanner. Its packet generator and receiver are optimized for discovery speed; service validation belongs in a slower second stage.

```mermaid
flowchart LR
    C["Allowlisted CIDRs"] --> G["Stateless packet generator"]
    G --> N["Network"]
    N --> R["Asynchronous receiver"]
    R --> O["Binary / JSON / XML output"]
    O --> V["Rate-limited validation stage"]
```

## Installation

```shell-session
operator@lab:~$ sudo apt install masscan
operator@lab:~$ masscan --version
Masscan version 1.3.x
```

For source builds, pin a release, review build instructions, and retain the binary checksum. Raw packet access normally requires privilege or narrowly assigned capabilities.

## Core syntax and controls

```bash
sudo masscan 192.0.2.0/28 -p22,80,443 --rate 100 --exclude 192.0.2.7 \
  --output-format json --output-filename evidence/masscan.json
```

| Purpose | Important options |
|---|---|
| Targets | CIDRs, `-iL`, `--exclude`, `--excludefile` |
| Ports | `-p80,443`, ranges, `U:53`, `--top-ports` where supported |
| Rate | `--rate`, `--max-rate` depending on version, `--wait` |
| Network | `--interface`, `--adapter-ip`, `--adapter-port`, `--adapter-mac`, `--router-mac` |
| Output | `-oL`, `-oJ`, `-oX`, `-oB`, `--output-format`, `--output-filename` |
| Operations | `--echo`, `--resume`, `--readscan`, `--retries`, `--seed` |
| Banners | `--banners`, protocol-specific hello controls where supported |

`--echo` prints a normalized configuration suitable for review. Put scope and exclusions in a configuration file and have a second operator check them before a high-rate run.

```shell-session
operator@lab:~$ sudo masscan --conf approved.conf --echo
rate = 100.00
ports = 22,80,443
range = 192.0.2.0/28
exclude = 192.0.2.7/32
operator@lab:~$ sudo masscan --conf approved.conf
Discovered open port 443/tcp on 192.0.2.10
Discovered open port 22/tcp on 192.0.2.12
```

## Engineering and safety

Masscan can overwhelm links, NAT tables, firewalls, low-power devices, and monitoring pipelines long before it saturates the scanning host. Calculate packet rate from the weakest path, start low, observe loss and security telemetry, and increase only with approval. Coordinate source addressing so responses route correctly. Cloud providers and ISPs may impose additional scanning rules.

The discovery-to-validation pipeline should deduplicate `(IP, protocol, port)`, preserve timestamps, and submit only confirmed in-scope endpoints to service enumeration. A missed result may mean packet loss rather than a closed port; repeated samples at safe rates improve confidence.

## Packet-rate engineering

Estimate load before execution. A SYN probe may be roughly 60–90 bytes on the wire after Ethernet/IP/TCP overhead, but replies, VLAN tags, tunneling, retransmissions, and monitoring copies add cost.

```text
100,000 packets/s × 84 bytes × 8 ≈ 67.2 Mbit/s outbound
At 1% reply rate, response bandwidth is small—but firewall state and IDS event volume may not be.
```

Rate is not the only pressure control. Source-port range, adapter queues, receive-side scaling, virtual-switch capacity, router ARP/neighbor limits, and sensor event ingestion can become bottlenecks.

## Crook2Root validation pipeline

```shell-session
operator@range:~$ sudo masscan --conf approved.conf -oB evidence/discovery.scan
operator@range:~$ masscan --readscan evidence/discovery.scan -oJ evidence/discovery.json
operator@range:~$ jq -r '.[] | select(.ports != null) | .ip as $ip | .ports[] | "\($ip):\(.port)"' evidence/discovery.json | sort -u
192.0.2.10:443
192.0.2.12:22
```

Feed these candidates to a deliberately slower verification stage. Compare sent/received counters, packet-capture samples, and a known-open canary service. Repeat a small slice at a lower rate; large disagreement indicates loss or path instability.

## Troubleshooting

- No replies: verify router MAC, interface, source IP, return route, hypervisor networking, and raw-packet privilege.
- Implausibly few results: lower rate, increase wait time, inspect drops, and sample with a stateful scanner.
- Duplicate/stale results: normalize protocol/IP/port and retain observation timestamps.
- Network complaints: stop immediately, preserve the configuration, and compare actual packet rate with approved rate.

---
> 🔼 Up: [[Network Discovery Tools]]
