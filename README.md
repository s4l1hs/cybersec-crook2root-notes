<div align="center">

# 🕵️‍♂️ Crook2Root

### A zero-to-mastery cybersecurity knowledge base — engineered as a strict **12-Domain, multiple-trees** architecture.

*From `Crook` — an absolute novice fumbling in the dark — to `Root` — the master who owns the machine, the network, and their own tracks.*

![Domains](https://img.shields.io/badge/Domains-12-8A2BE2?style=for-the-badge)
![Notes](https://img.shields.io/badge/Notes-76%2B-00BFFF?style=for-the-badge)
![Built for](https://img.shields.io/badge/Built%20for-Obsidian-7C3AED?style=for-the-badge&logo=obsidian)
![Focus](https://img.shields.io/badge/Focus-Offensive%20%7C%20Defensive-DC143C?style=for-the-badge)
![License](https://img.shields.io/badge/License-Educational-50C878?style=for-the-badge)

</div>

---

## 🎯 The Vision

**Crook2Root** is not a pile of notes — it is a **deliberately engineered graph**. Most security wikis collapse into an unreadable hairball because every note links to every other note. Crook2Root rejects that. It is built as **twelve independent, colour-coded trees**, each a self-contained discipline, all hanging off a single Root:

```
🌐 Root  →  🗂️ Hub (Domain)  →  🌿 Branch (Sub-topic)  →  📄 Master Note (Deep dive)
```

Every note declares exactly **one** parent. There are **no lateral links between master notes** — only clean, vertical, four-level hierarchy. The result is a knowledge base you can *see*: open the Obsidian Graph View and twelve distinct, colour-separated constellations light up, each a domain you can master one branch at a time.

---

## 🌳 The 12-Domain Architecture

```mermaid
flowchart TD
    R["🌐 Cyber Security Root"]
    R --> D1["🌐 Networking"]
    R --> D2["💻 OS Internals"]
    R --> D3["🔐 Cryptography"]
    R --> D4["⚔️ Offensive Security"]
    R --> D5["🛡️ Defensive Security"]
    R --> D6["🕸️ Application Security"]
    R --> D7["⚙️ DevSecOps"]
    R --> D8["🧰 Tooling & Scripting"]
    R --> D9["☁️ Cloud Security"]
    R --> D10["🦠 Reverse Engineering & Malware"]
    R --> D11["🔌 Hardware & IoT Security"]
    R --> D12["🧠 AI Security"]
    style R fill:#111,stroke:#fff,stroke-width:2px,color:#fff
```

| # | Domain | Colour | What lives here |
|:-:|:--|:--|:--|
| 01 | 🌐 **Networking** | `#42D4F4` Cyan | OSI/TCP-IP, addressing & subnetting, DNS/DHCP/NAT, routing, core protocols |
| 02 | 🐧 **OS Internals** | `#FFA500` Orange | The CLI arsenal, filesystem, permissions & processes, privilege escalation, Windows/AD internals |
| 03 | 🔐 **Cryptography** | `#FFE119` Yellow | Encoding vs encryption vs hashing, symmetric/asymmetric, signatures, TLS/JWT, cracking |
| 04 | ⚔️ **Offensive Security** | `#E6194B` Red | Methodology, recon, social engineering, exploitation, exploit dev, anti-forensics, CTF & stego |
| 05 | 🛡️ **Defensive Security** | `#4363D8` Blue | Controls & hardening, logging, EDR/SIEM, detection engineering |
| 06 | 🕸️ **Application Security** | `#911EB4` Purple | HTTP fundamentals, the OWASP Top 10, web exploitation, API defense |
| 07 | ⚙️ **DevSecOps** | `#3CB44B` Green | Containers, orchestration, secure delivery pipelines |
| 08 | 🧰 **Tooling & Scripting** | `#469990` Teal | Python & Go for security, the core pentest toolkit |
| 09 | ☁️ **Cloud Security** | `#FABED4` Pink | IAM, storage, workloads, cloud-native attack paths *(growing)* |
| 10 | 🦠 **Reverse Engineering & Malware** | `#BFEF45` Lime | Disassembly, debugging, malware analysis *(growing)* |
| 11 | 🔌 **Hardware & IoT Security** | `#9A6324` Brown | Firmware, embedded, radio, IoT *(growing)* |
| 12 | 🧠 **AI Security** | `#F032E6` Magenta | Prompt injection, model theft, data poisoning *(growing)* |

> Colours are wired into `.obsidian/graph.json` and applied automatically via per-domain `#tree/*` tags — **open the graph and the twelve trees are instantly colour-coded.**

---

## ⚙️ Real-World Application

This vault is academic and **optimized for practice**:

- **⚔️ Offensive research on bare-metal, native Linux.** The material assumes a real attack host — e.g. a **ThinkPad running BlackArch** — where native hardware access unlocks Wi-Fi monitor mode & injection, full-speed GPU hash cracking, and USB/SDR tooling that virtual machines cripple. Bare-metal isn't a preference here; it's a capability.
- **🏗️ Engineering secure backends & architectures.** The DevSecOps, Application Security, Cryptography, and Tooling trees are written for builders — hardening containers, choosing the right KDF, defending APIs, and designing systems that survive the very attacks documented in the offensive trees.

Offense and defense are two views of one skill: **every offensive technique is paired with how it is detected and defeated.**

---

## 🚀 How to Use

This is an **Obsidian vault**. To get the full experience — the interlinked graph and the automatic colours:

```bash
# 1. Clone the repository
git clone https://github.com/s4l1hs/cybersec-crook2root-notes.git

# 2. Open Obsidian → "Open folder as vault" → select the cloned folder
```

3. Open the **Graph View** (`Ctrl/Cmd + G`).
4. The **twelve domains render in their distinct colours automatically** (config ships in `.obsidian/`).
5. Start at **`Cyber Security`** and explore a tree, or dive straight into a domain hub.

> 💡 **Tip:** If colours don't appear immediately, fully **quit and reopen** Obsidian once so it loads `.obsidian/graph.json`.

**Navigation model:** `Root → Hub → Branch → Master Note`. Pick a domain, follow a branch, go deep. The graph is your map.

---

## 📊 At a Glance

| | |
|:--|:--|
| 🗂️ **Domains** | 12 colour-coded trees |
| 🌿 **Structure** | Root → Hub → Branch → Master (4 levels, strictly hierarchical) |
| 📄 **Notes** | 76+ interconnected Markdown notes |
| 🖼️ **Assets** | 200+ diagrams & images (self-authored SVGs + localized graphics) |
| 🔗 **Integrity** | 0 broken links · 0 broken anchors · 0 broken images |

---

## ⚖️ Ethics & Authorized Use

Crook2Root documents offensive techniques **so they can be understood, detected, and defended against.** Everything here is for **authorized engagements, CTFs, personal labs, and defensive research** on systems you own or are explicitly permitted to test. Using these techniques against systems without permission is illegal. **Learn to attack so you can build the unbreakable.**

---

## 🤝 Contributing

Crook2Root is designed to grow with the community. The four newest domains — **Cloud Security, Reverse Engineering & Malware, Hardware & IoT, and AI Security** — are live scaffolds ready for their first branches. Contributions that respect the architecture are welcome:

1. One note = one topic; give it a single parent (`Domain:` frontmatter).
2. Tag it with its domain's `#tree/*` tag so it inherits the colour.
3. **No lateral links between master notes** — reference other topics in **bold**, not `[[wikilinks]]`.
4. Keep it *crook2root*: start from zero, end at mastery, show real commands.

---

<div align="center">

**Twelve trees. One root. Crook → Root.**

</div>
