---
title: "LNX.3 I/O Redirection & Piping"
aliases: ["IO Redirection", "Piping", "Pipes", "Command Chaining", "Redirection"]
tags:
  - tree/linux
  - cyber/foundations/linux
  - type/technique
  - level/apprentice
Domain:
  - "[[Branch Shell Power]]"
Color: "#F58231"
---

# 🔗 LNX.3 · I/O Redirection & Piping

> [!abstract] Master Note of [[Branch Shell Power]]
> The Unix philosophy: small tools that each do one thing, **composed** into something powerful. Redirection and pipes are the glue. Master these operators and you can answer almost any question about a system in a single line — the jump from *typing* commands to *engineering* them.

## The three streams
Every process has three default channels, each with a **file descriptor** number:

![[lnx_io_streams.svg]]

- **stdin (0)** — input, normally the keyboard.
- **stdout (1)** — normal output, normally the screen.
- **stderr (2)** — error output, also the screen but a *separate* stream (so you can split errors from results).

Redirection rewires these streams to/from files; pipes wire one process's stdout into the next's stdin.

## The operators
| Operator | Meaning | Example |
| --- | --- | --- |
| `\|` | **Pipe** — stdout of left → stdin of right | `cat f \| grep err` |
| `>` | Redirect stdout to a file (**overwrite**) | `nmap ... > scan.txt` |
| `>>` | Redirect stdout to a file (**append**) | `echo done >> log` |
| `<` | Feed a file into stdin | `mysql db < dump.sql` |
| `2>` | Redirect **stderr** | `find / 2> /dev/null` |
| `&>` / `>&` | Redirect **both** stdout+stderr | `cmd &> all.log` |
| `2>&1` | Send stderr to *wherever stdout goes* | `cmd > all.log 2>&1` |
| `;` | Run sequentially, **regardless** of success | `id; hostname` |
| `&&` | Run next **only if** previous **succeeded** | `make && ./run` |
| `\|\|` | Run next **only if** previous **failed** | `ping -c1 h \|\| echo DOWN` |
| `&` | Run the command in the **background** | `python3 -m http.server &` |

## Why `2>/dev/null` is everywhere
`find` and friends spam `Permission denied` to **stderr** for every directory you can't read. Sending stream **2** to the black-hole device `/dev/null` discards that noise, leaving only real results on stdout:
```shell-session
$ find / -perm -4000 -type f 2>/dev/null      # only the SUID hits, no error flood
/usr/bin/sudo
/usr/bin/passwd
```

## The glue tools you pipe into
| Tool | Job | Example |
| --- | --- | --- |
| `grep` | keep matching lines | `... \| grep 200` |
| `cut -d: -f1` | slice columns by delimiter | `cut -d: -f1 /etc/passwd` |
| `awk '{print $1}'` | field-aware text processing | `... \| awk '{print $9}'` |
| `sed 's/a/b/g'` | stream find-and-replace | `... \| sed 's/ /_/g'` |
| `sort` / `uniq -c` | order / count duplicates | `sort \| uniq -c` |
| `tr` | translate/delete chars | `tr 'A-Z' 'a-z'` |
| `tee file` | write to file **and** screen | `... \| tee out.txt` |
| `xargs` | turn stdin into arguments | `... \| xargs -I{} curl {}` |

## Practical chained one-liners
```shell-session
# Top 10 IPs hitting a web server, by hit count
$ cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -rn | head
   4213 10.0.0.9
    880 81.143.211.90

# Every unique user on the box, from /etc/passwd
$ cut -d: -f1 /etc/passwd | sort

# Hunt secrets across a web root, keep a copy while watching it scroll
$ grep -rniE "password|api[_-]?key|secret" /var/www 2>/dev/null | tee creds.txt

# Only exploit if the host is alive
$ ping -c1 10.10.10.5 &>/dev/null && echo "[+] up, launching" || echo "[-] down"

# Download every URL listed in a file, 4 at a time
$ cat urls.txt | xargs -P4 -I{} wget -q {}

# Extract all IPv4 addresses from any text
$ grep -oE '([0-9]{1,3}\.){3}[0-9]{1,3}' file | sort -u
```

## Command & process substitution
- **`$(...)`** — run a command and drop its **output** in place:
  ```shell-session
  $ kill $(pgrep -f suspicious.py)     # kill by matched name
  $ echo "Kernel: $(uname -r)"         # embed output in a string
  ```
- **`<(...)`** — turn a command's output into a **file** an argument can read:
  ```shell-session
  $ diff <(ls dir1) <(ls dir2)         # compare two listings with no temp files
  ```

## Here-docs & here-strings — feed multi-line input
```shell-session
$ cat <<'EOF' > payload.py             # write a block to a file
import os; os.system("/bin/sh")
EOF
$ base64 -d <<< "cGFzc3dk"             # here-string: feed one line to stdin
passwd
```

> [!tip] Crook → Root
> **Crook** runs `grep`, copies the result, runs the next command by hand. **Root** pipes the whole investigation into one expression — `cut | sort | uniq -c | sort -rn` is a reflex, not a lookup. Composition *is* the skill.

---
> 🔼 Up: [[Branch Shell Power]]
