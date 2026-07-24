---
title: "Memory Management and Paging"
aliases: ["Memory Management", "Virtual Memory", "Paging", "TLB", "Page Fault", "Heap", "Stack", "ASLR"]
tags:
  - tree/os
  - cyber/foundations/theory
  - type/concept
  - level/root
Domain:
  - "[[OS Theory and Architecture]]"
Color: "#FFA500"
---

# 🧠 Memory Management and Paging

> [!abstract] Note of [[OS Theory and Architecture]]
> Every pointer bug, every buffer overflow, every ASLR bypass is a story about **memory management**. Understand virtual memory, paging, and the heap/stack layout and exploitation stops being incantation — it becomes physics.

## Virtual memory — the great illusion
Each process sees a private, contiguous **virtual address space**; the CPU's **MMU** translates virtual → physical addresses via **page tables**, at a fixed granularity (usually **4 KB pages**). Benefits that are also security boundaries:
- **Isolation:** process A cannot name process B's physical memory.
- **Over-commit:** total virtual memory can exceed RAM (backed by the **pagefile/swap**).
- **Permissions per page:** Read / Write / **eXecute** bits — the hardware basis of **DEP/NX**.

## Paging, page faults, and the TLB
- **Page table walk:** translating an address means walking a multi-level table — slow, so the **TLB (Translation Lookaside Buffer)** caches recent translations. TLB behaviour leaks addresses in side channels.
- **Page fault:** accessing a page not resident in RAM traps to the kernel, which either loads it from disk (**demand paging** — valid) or kills the process (**segfault / access violation** — invalid). *Every crash you cause during fuzzing is a page fault the kernel didn't like.*
- **Swapping/thrashing:** under memory pressure the kernel evicts pages; excessive faulting is thrashing. Secrets swapped to disk are a forensic and theft risk (hence `mlock()` for keys).

## The process address space
```
High addr ┌───────────────┐
          │  Stack   ↓     │  ← local vars, return addresses, saved regs (grows down)
          │      ...       │
          │  Heap    ↑     │  ← malloc/new, dynamic (grows up)
          │  BSS / Data    │  ← globals
          │  Text (code)   │  ← executable, usually read-only
Low  addr └───────────────┘
```
- **Stack:** LIFO, per-thread; holds return addresses → the target of the classic **stack buffer overflow** (overwrite the saved return address → hijack control flow).
- **Heap:** dynamically allocated; managed by an allocator (ptmalloc, tcmalloc). Corrupting allocator metadata (**heap overflow**, **use-after-free**, **double-free**) yields powerful primitives.

## Where this *is* exploitation
> [!warning] Exploit development lineage (authorized study)
> Memory theory maps 1:1 to the offense/defense arms race — the deep dives live in the Offensive tree's **Exploit Development** material:
> | Concept | Attack | Mitigation |
> | --- | --- | --- |
> | Stack return address | **Buffer overflow** → overwrite saved RIP | **Stack canaries**, bounds checks |
> | Executable data pages | inject + run shellcode | **DEP / NX** (W^X) |
> | Predictable addresses | hardcode gadget/return addrs | **ASLR** (randomise base addresses) |
> | DEP defeats shellcode | reuse existing code | **ROP** → and **CFI/CET** to stop it |
> | Freed heap object reused | **use-after-free** | safe allocators, GC, `MTE` |
> | Uninitialised/over-read | **memory leak** (e.g. Heartbleed) → defeats ASLR by disclosing addresses | zeroing, bounds checks |
>
> Note the loop: **ASLR** relies on secrecy of addresses, so a **memory-leak** primitive (info disclosure) is the master key that re-enables everything else.

**Microarchitectural attacks** (**Meltdown/Spectre**) abuse speculative execution + the TLB/cache to read memory across the very isolation boundary paging is supposed to enforce — theory failing at the silicon level.

> [!tip] Crook → Root
> **Root** reads a crash as a page fault with a story, knows W^X/ASLR/canaries are *layers* (each defeated by a specific primitive), and understands that an **info leak** is worth more than a write because it collapses ASLR.

---
> 🔼 Up: [[OS Theory and Architecture]]
