---
title: "LNX.5 Permissions & Process Management"
aliases: ["Linux Permissions", "chmod", "chown", "RWX", "Process Management", "ps", "kill"]
tags:
  - tree/os
  - cyber/foundations/linux
  - type/technique
  - level/apprentice
Domain:
  - "[[Linux]]"
Color: "#FFA500"
---

# 🔐 LNX.5 · Permissions & Process Management

> [!abstract] Master Note of [[Linux]]
> Permissions are the heart of Linux security — and the root of most privilege escalation. Processes are the system in motion. Control both and you control the box.

## Reading the permission string
`ls -l` shows a 10-character mode. Learn to read it at a glance:

![[lnx_permission_bits.svg]]

```
-rwxr-x---  1  alice  devs  4096  Jul 18  script.sh
│└┬┘└┬┘└┬┘      owner  group
│ │  │  └ others : ---   (no access)
│ │  └── group  : r-x   (read + execute)
│ └───── owner  : rwx   (read + write + execute)
└─────── type   : - file   d dir   l symlink   c/b device
```
Three triads — **owner / group / others** — each granting **r**ead, **w**rite, e**x**ecute. On a **directory**, `x` means "may enter/traverse" and `w` means "may create/delete files inside" (so write on a dir is powerful even without write on its files).

## Octal — the number every admin thinks in
Each permission is a bit: **r=4, w=2, x=1**. Add them per triad:
| Octal | Symbolic | Meaning |
| --- | --- | --- |
| `7` | `rwx` | read + write + execute |
| `6` | `rw-` | read + write |
| `5` | `r-x` | read + execute |
| `4` | `r--` | read only |
| `0` | `---` | nothing |

So `chmod 750` = owner `rwx` (7), group `r-x` (5), others `---` (0).

## chmod & chown
```shell-session
$ chmod 750 script.sh        # numeric (absolute)
$ chmod u+x,g-w,o-r file     # symbolic: +x owner, ‑w group, ‑r others
$ chmod +x exploit.sh        # make a script runnable
$ chmod -R 755 /var/www      # recursive
$ chown alice:devs file      # set owner:group   (root only)
$ chgrp devs file            # set group only
$ umask 027                  # default mask → new files 640, dirs 750
```
> [!danger] `chmod 777` is a finding
> "Everyone can read, write, and execute" is almost always a misconfiguration. A world-writable script run by root is a straight path to privilege escalation (see **Advanced Mechanics & PrivEsc**). Least privilege always.

## The special bits
Beyond `rwx`, three bits change *who/how* a program runs:
| Bit | Octal | Effect | In `ls -l` |
| --- | --- | --- | --- |
| **SUID** | `4000` | run as file **owner** (often root) | `-rwsr-xr-x` (`s` in owner-exec) |
| **SGID** | `2000` | run as file **group**; on a dir, new files inherit the group | `-rwxr-sr-x` |
| **Sticky** | `1000` | in a dir, only a file's **owner** can delete it (e.g. `/tmp`) | `drwxrwxrwt` (`t` in others-exec) |

SUID/SGID are the single most important Linux privesc concept — they get the full treatment (with exploitation) in **Advanced Mechanics & PrivEsc**. Here, just *recognise* the `s`: `-rwsr-xr-x` means SUID is set.

## Process management
A **process** is a running program with a **PID** and an owner. Managing them is core to both operating a box and hunting intruders.
```shell-session
$ ps aux                    # EVERY process: user, PID, %CPU/MEM, full command line
root  931  0.0  0.2  /usr/sbin/sshd -D
www   1440 2.1  1.0  /usr/sbin/apache2 -k start
$ ps -ef                    # alternate format, shows parent PID (PPID)
$ pstree -p                 # the process TREE with PIDs (see parent→child)
$ top          # or htop     # live, sorted resource view — q to quit
$ pgrep -af nginx           # find PIDs by name, with command line
```
`ps aux` is a recon goldmine — it reveals **services running as root** and can catch **credentials passed on a command line** (`mysql -u root -pS3cret`).

### Signals — how you talk to a process
`kill` sends a **signal**, not necessarily "death":
| Signal | Number | Effect |
| --- | --- | --- |
| `SIGTERM` | 15 | polite "please stop" (default) — allows cleanup |
| `SIGKILL` | 9 | forced, uncatchable kill — last resort |
| `SIGHUP` | 1 | hang-up — many daemons **reload config** on this |
| `SIGSTOP`/`SIGCONT` | 19/18 | pause / resume |

```shell-session
$ kill 24507                # SIGTERM by PID
$ kill -9 24507            # SIGKILL (force)
$ pkill -f suspicious.py    # kill by matched command line
$ killall firefox           # kill every process by exact name
```

### Job control & backgrounding
```shell-session
$ python3 -m http.server 8000 &   # start in background (&) → prints a job id
[1] 24507
$ jobs                            # list this shell's background jobs
$ fg %1                           # bring job 1 to the foreground
$ bg %1                           # resume a stopped job in the background
$ nohup ./long_scan.sh &          # keep running after you log out
$ disown -h %1                    # detach a job from the shell
```
Press **`Ctrl+Z`** to suspend the foreground job, then `bg` to continue it in the background — the everyday way to free your prompt without killing the task.

> [!tip] Crook → Root
> **Crook** reboots when something hangs. **Root** finds the offender with `ps aux | grep`, understands *why* it runs and as whom, and `kill`s precisely — or, on the blue-team side, spots the rogue process that shouldn't be there at all (a shell spawned by a web server is a red flag).

---
> 🔼 Up: [[Linux]]
