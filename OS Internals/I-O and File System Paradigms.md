---
title: "I-O and File System Paradigms"
aliases: ["IO", "VFS", "inodes", "Interrupts", "DMA", "File System Theory"]
tags:
  - tree/os
  - cyber/foundations/theory
  - type/concept
  - level/operator
Domain:
  - "[[OS Theory and Architecture]]"
Color: "#FFA500"
---

# 🧠 I/O and File System Paradigms

> [!abstract] Note of [[OS Theory and Architecture]]
> The OS's job is to mediate between fast CPUs and slow, messy devices. The abstractions it uses — the **VFS**, **inodes**, **interrupts**, and **DMA** — are exactly the structures forensic analysts reconstruct and hardware attackers subvert.

## The Virtual File System (VFS)
The **VFS** is an abstraction layer that lets one set of syscalls (`open`, `read`, `write`, `close`) work across wildly different backends (ext4, NTFS, APFS, NFS, `/proc`). Its core objects:
- **inode** — the metadata record for a file: owner, permissions, size, timestamps (MAC — modify/access/change), link count, and **pointers to data blocks**. The *name* is not in the inode; directories map names → inode numbers.
- **dentry** — a cached name↔inode mapping.
- **file descriptor** — a per-process handle (an int indexing the fd table) to an open file.

> Because a filename is just a directory entry pointing to an inode, **hard links** are multiple names for one inode, and **deleting** a file only unlinks the name — the data blocks persist until the link count hits zero, which is why "deleted" data is recoverable.

## Interrupts vs polling vs DMA
How the CPU learns a device is ready:
| Method | Mechanism | Cost |
| --- | --- | --- |
| **Polling** | CPU repeatedly checks status | wastes cycles |
| **Interrupt-driven** | device raises an **IRQ**; CPU jumps to an **ISR** | efficient; ISR latency matters |
| **DMA** | a **DMA controller** moves data device↔RAM **without the CPU**, then interrupts once done | frees the CPU for bulk transfers |

An **interrupt** preempts whatever's running, saves context, runs the **Interrupt Service Routine**, and returns — the same save/restore machinery as a context switch, and a source of timing side-channels.

## Where this becomes security
> [!warning] Hardware & forensic angles (authorized study)
> - **DMA attacks:** because DMA bypasses the CPU (and historically the MMU), a malicious peripheral over **Thunderbolt/FireWire/PCIe** can read/write physical RAM directly — stealing keys or patching the kernel (`PCILeech`, "Thunderclap"). **Defense:** the **IOMMU** (VT-d/AMD-Vi) sandboxes device memory access; **Kernel DMA Protection**.
> - **Forensics from inodes:** the MAC timestamps, link counts, and the **journal** (`$LogFile`/ext4 journal) rebuild the timeline; **file slack** and unallocated blocks hold "deleted" data. Anti-forensics (timestomping, secure delete) targets exactly these structures — and journaling/off-host copies defeat it.
> - **Filesystem TOCTOU:** path-based operations that resolve a name twice race against symlink swaps (ties to the **Race Conditions** theory).
> - **Interrupt/`/proc` side-channels:** interrupt counts and I/O timing (`/proc/interrupts`, procfs) leak activity across isolation boundaries.

## Buffering, caching, and the page cache
The kernel caches file data in the **page cache** to hide disk latency; writes are buffered and flushed later (`fsync` forces durability). This cache is a double-edged sword: it speeds everything up, but cached secrets live in RAM (recoverable via memory forensics), and cache-timing reveals which files were accessed.

> [!tip] Crook → Root
> **Root** knows a filename is just a pointer to an **inode** (so "deleted" ≠ gone), treats **DMA** as a path around the CPU that only the **IOMMU** guards, and reads inode timestamps + the journal as the ground truth of what really happened on a disk.

---
> 🔼 Up: [[OS Theory and Architecture]]
