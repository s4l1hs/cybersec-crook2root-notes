---
title: "Bash for Security Operations"
aliases: ["Bash Security Automation"]
tags: [tree/tooling, cyber/tooling/programming/bash, level/operator]
Domain: "[[Programming for Security]]"
Color: "#708090"
---

# Bash for Security Operations

Bash is the orchestration language of Unix operations. It is strongest when composing trusted programs and weakest when parsing complex untrusted data. Quote expansions, make failures explicit, and graduate to Python or Go when state or input grammar becomes complex.

```mermaid
flowchart LR
    A["Arguments"] --> V["Validate and quote"]
    V --> C["Compose commands"]
    C --> T["Temporary workspace"]
    T --> E["Evidence + exit status"]
    E --> X["Trap cleanup"]
```

## Safe script skeleton

```bash
#!/usr/bin/env bash
set -Eeuo pipefail
IFS=$'\n\t'

usage() { printf 'usage: %s INPUT OUTPUT\n' "${0##*/}" >&2; exit 2; }
[[ $# -eq 2 ]] || usage
input=$1; output=$2
[[ -r "$input" ]] || { printf 'not readable: %s\n' "$input" >&2; exit 3; }

workdir=$(mktemp -d)
cleanup() { rm -rf -- "$workdir"; }
trap cleanup EXIT INT TERM

awk 'NF && $1 !~ /^#/' "$input" | sort -u > "$workdir/normalized.txt"
install -m 0600 "$workdir/normalized.txt" "$output"
sha256sum -- "$output"
```

```shell-session
analyst@lab:~$ ./normalize.sh scope.txt scope.normalized
f54c01...  scope.normalized
analyst@lab:~$ stat -c '%a %n' scope.normalized
600 scope.normalized
```

`set -e` alone is not enough; `-u` rejects unset variables, `pipefail` propagates pipeline failures, and `-E` preserves error traps in functions. Quote `"$var"`, use arrays for command arguments, place `--` before path operands, and never evaluate untrusted text.

## Arrays and parallel control

```bash
targets=("192.0.2.10" "192.0.2.20")
ports=(22 443)
for target in "${targets[@]}"; do
  for port in "${ports[@]}"; do
    timeout 2 bash -c 'exec 3<>/dev/tcp/$1/$2' _ "$target" "$port" \
      && printf '%s,%s,open\n' "$target" "$port" \
      || printf '%s,%s,closed-or-filtered\n' "$target" "$port"
  done
done
```

For concurrency, prefer `xargs -P` with a small fixed count and exported functions, or GNU Parallel with an explicit jobs limit. Capture stderr separately and preserve exit codes.

## Defensive evidence triage

```shell-session
analyst@sensor:~$ journalctl --since '2026-07-30 08:00:00' --until '2026-07-30 09:00:00' -o json \
  | jq -r 'select(._SYSTEMD_UNIT=="sshd.service") | [."__REALTIME_TIMESTAMP", .MESSAGE] | @tsv' \
  | head
1753862601000000 Accepted publickey for analyst from 192.0.2.44 port 51218 ssh2
```

Avoid `for x in $(command)` because word splitting corrupts spaces and glob characters. Prefer `while IFS= read -r line`. Use `shellcheck` and `shfmt` in CI.

```shell-session
engineer@lab:~$ shellcheck normalize.sh
engineer@lab:~$ bash -n normalize.sh
```

No output means both checks passed.

---
> 🔼 Up: [[Programming for Security]]
