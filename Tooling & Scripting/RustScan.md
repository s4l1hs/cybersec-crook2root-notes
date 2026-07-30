---
title: "RustScan"
aliases: ["Rust Port Scanner"]
tags: [tree/tooling, cyber/tooling/offensive/rustscan, level/operator]
Domain: "[[Network Discovery Tools]]"
Color: "#708090"
---

# RustScan

RustScan is a fast TCP port-discovery front end that commonly passes discovered ports to Nmap for deeper service enumeration. It separates rapid socket discovery from fingerprinting.

```mermaid
flowchart LR
    I["In-scope hosts"] --> R["RustScan connect workers"]
    R --> P["Open-port set"]
    P --> N["Nmap arguments after --"]
    N --> E["Service evidence"]
```

## Installation

Install from a trusted release/package source, verify its checksum, then record the version.

```shell-session
operator@lab:~$ rustscan --version
rustscan 2.x
operator@lab:~$ rustscan --help | head
Fast Port Scanner built in Rust
```

## Core options

| Purpose | Common option |
|---|---|
| Address | `-a`, `--addresses` |
| Port selection | `-p`, `--ports`, `--range` |
| Batch size | `-b`, `--batch-size` |
| Per-socket timeout | `-t`, `--timeout` |
| Retries | `--tries` |
| File input | `--addresses` with supported list syntax/version |
| No Nmap | `--scripts none` or current version equivalent |
| Nmap handoff | arguments after `--` |
| Configuration | user config/TOML path for the installed version |

Confirm flags against `rustscan --help`; the project’s CLI has changed across releases.

```shell-session
operator@lab:~$ rustscan -a 192.0.2.10 -p 22,80,443 -b 100 -t 1500 -- -sV --reason -oN evidence/web01.nmap
Open 192.0.2.10:22
Open 192.0.2.10:443
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 9.6
443/tcp open  https   nginx 1.24
```

## Tuning and interpretation

Batch size controls simultaneous socket attempts; too high causes local file-descriptor errors, NAT pressure, dropped traffic, and false negatives. Timeout must reflect path latency. Begin with a small batch, inspect `ulimit -n`, and compare a sample with a slower scanner.

RustScan discovers TCP reachability; it does not validate business risk. The Nmap handoff is convenient but must remain rate-limited and scope-safe. Preserve the exact RustScan and downstream arguments because the combined pipeline otherwise becomes difficult to reproduce.

```shell-session
operator@lab:~$ ulimit -n
1024
operator@lab:~$ time rustscan -a 192.0.2.10 -p 1-1024 -b 50 -t 2000 --scripts none
Open 192.0.2.10:22
Open 192.0.2.10:443
```

## Configuration & reproducibility

Treat the user configuration as code. Record addresses, ranges, timeout, batch size, retry count, port order, scripts mode, and downstream arguments. A comfortable laptop default may be destructive through a VPN or against embedded assets.

```text
run_id: rs-20260730-01
targets: 192.0.2.10/32
ports: 1-1024
batch_size: 50
timeout_ms: 2000
tries: 1
downstream: -sV --reason
```

## Crook2Root tuning lab

Run the same isolated target at batch sizes 10, 50, 200, and 1,000. Capture elapsed time, local socket errors, target logs, and confirmed ports. Plot speed against false-negative/error rate; the fastest run is not automatically the best run.

```shell-session
operator@range:~$ for b in 10 50 200; do /usr/bin/time -f "batch=$b elapsed=%e" rustscan -a 192.0.2.10 -p 1-1024 -b "$b" -t 2000 --scripts none; done
batch=10 elapsed=4.72
batch=50 elapsed=1.21
batch=200 elapsed=0.48
```

Validate the open-port set with a packet trace or stateful scan. If higher batches lose a known canary port, local or network pressure has invalidated the optimization.

## Failure analysis

`Too many open files` indicates local descriptor pressure; timeouts across all ports suggest routing/filtering; inconsistent results suggest timeout or batch oversubscription. If downstream enumeration fails, copy the generated command, run it separately, and distinguish discovery failure from handoff syntax or Nmap privilege problems.

---
> 🔼 Up: [[Network Discovery Tools]]
