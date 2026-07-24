---
title: "Linux Kernel Internals"
aliases: ["Linux Kernel", "eBPF", "cgroups", "namespaces", "Kernel Panic", "LKM"]
tags:
  - tree/os
  - cyber/foundations/linux
  - type/concept
  - level/root
Domain:
  - "[[Linux]]"
Color: "#FFA500"
---

# 🐧 Linux Kernel Internals

> [!abstract] Note of [[Linux]]
> Above the CLI sits the kernel — and the modern security battleground is here: **namespaces** and **cgroups** (the primitives that *are* containers), **eBPF** (the programmable observability/enforcement layer every serious tool now uses), loadable modules, and the panics that tell you something went very wrong.

## User space vs kernel space
Processes run in **user space** (ring 3) and cross into **kernel space** (ring 0) via **syscalls** (`man 2 syscalls`, traced with `strace`). The kernel exposes itself through virtual filesystems: **`/proc`** (process/kernel state), **`/sys`** (device/kernel objects), **`/dev`**. This is the interface attackers and defenders both read.

## Namespaces — the "what a process can see" primitive
Namespaces virtualise global resources so a process gets its own view. Seven kinds: **mount, PID, network, UTS, IPC, user, cgroup**.
```bash
lsns                                   # list namespaces
unshare --user --map-root-user --net bash   # new user+net namespace
nsenter -t <pid> -n ip addr            # enter a container's network ns
```
> **User namespaces** let an unprivileged user be "root" *inside* the namespace — a powerful isolation tool and a recurring **privilege-escalation** surface (many LPE CVEs abuse `CLONE_NEWUSER`). Containers = namespaces + cgroups + capabilities; a container escape is usually a namespace/capability misconfiguration.

## cgroups — the "how much a process can use" primitive
**Control groups (v2)** limit and account CPU, memory, PIDs, and I/O per process group — the resource half of containers.
```bash
cat /sys/fs/cgroup/cgroup.controllers          # available controllers
systemd-cgls ; systemctl status <svc>          # cgroup tree
```
The classic **`release_agent` container-escape** abused a writable cgroup `release_agent` to run code on the host — a canonical example of why cgroup/namespace config is security-critical.

## Capabilities — root, decomposed
Instead of all-or-nothing root, the kernel splits privilege into **capabilities** (`man 7 capabilities`). `CAP_SYS_ADMIN` ("the new root"), `CAP_SYS_PTRACE`, `CAP_NET_RAW`, `CAP_SETUID`.
```bash
getcap -r / 2>/dev/null                 # files with capabilities (privesc hunt)
capsh --print                            # current process capabilities
```

## eBPF — the programmable kernel
**eBPF** runs sandboxed, verified programs inside the kernel on hooks (syscalls, network, tracepoints) without loading a module. It powers modern **observability** (`bpftrace`, `bcc`), **networking** (Cilium), and **security/EDR** (Falco, Tetragon) — and, in offensive research, **stealthy rootkits/hooks**.
```bash
sudo bpftrace -e 'tracepoint:syscalls:sys_enter_execve { printf("%s %s\n", comm, str(args->filename)); }'
sudo execsnoop-bpfcc                     # every exec, live (bcc)
```
> eBPF is double-edged: the same hook that gives a defender per-syscall visibility gives an attacker a kernel-resident vantage point — hence eBPF programs themselves are now audited.

## Loadable Kernel Modules (LKMs)
Drivers/features loaded at runtime — and the classic **kernel rootkit** vector.
```bash
lsmod ; modinfo <mod> ; sudo insmod x.ko ; sudo rmmod x
```
On modern systems **Secure Boot + module signing** block unsigned modules. Kernel exploits (LPE) often target a driver or a race to load code at ring 0.

## Kernel panics & Oops
- An **Oops** is a recoverable kernel error (kills the offending task, logs a stack trace to `dmesg`); a **panic** is unrecoverable (system halts).
- Read them via `dmesg`, `/var/log/kern.log`, or persisted **kdump**/`pstore` crash dumps.
```bash
dmesg -T | grep -iE 'oops|panic|BUG|segfault'
```
> **Defensive signal:** repeated Oopses/segfaults on a service are a fingerprint of **exploit development / fuzzing** in progress (see the Offensive tree's exploit-dev notes).

> [!tip] Crook → Root
> **Root** knows containers are just **namespaces + cgroups + capabilities**, reaches for **eBPF** to watch every `execve` live, hunts file **capabilities** for privesc, and reads `dmesg` panics as either a bug — or someone building an exploit.

---
> 🔼 Up: [[Linux]]
