---
title: "ffuf"
aliases: ["Fuzz Faster U Fool"]
tags: [tree/tooling, cyber/tooling/offensive/ffuf, level/master]
Domain: "[[Enumeration & Service Interaction Tools]]"
Color: "#708090"
---

# ffuf

ffuf is a high-performance HTTP fuzzer that inserts the `FUZZ` keyword into URLs, headers, bodies, or multiple positions and filters responses by measurable properties.

```mermaid
flowchart LR
    P["Request template with FUZZ"] --> E["Payload engine"]
    E --> H["HTTP workers"]
    H --> M["Matchers"]
    M --> F["Filters"]
    F --> O["JSON / CSV / HTML evidence"]
```

## Installation

```shell-session
operator@lab:~$ go install github.com/ffuf/ffuf/v2@latest
operator@lab:~$ ffuf -V
ffuf version: 2.x
```

Pin a release in production automation instead of installing an unversioned moving target.

## Inputs, request controls, matching, and filtering

| Area | Important flags |
|---|---|
| Input | `-w file[:KEYWORD]`, multiple `-w`, `-mode clusterbomb|pitchfork|sniper`, `-input-cmd`, `-input-num` |
| HTTP | `-u`, `-X`, `-H`, `-d`, `-b`, `-request`, `-request-proto`, `-r`, `-timeout`, `-x` proxy |
| Match | `-mc`, `-ms`, `-mw`, `-ml`, `-mr`, `-mt` |
| Filter | `-fc`, `-fs`, `-fw`, `-fl`, `-fr`, `-ft` |
| Rate | `-t`, `-rate`, `-p` delay/range, `-maxtime`, `-maxtime-job` |
| Output | `-o`, `-of json|ejson|html|md|csv|ecsv`, `-od`, `-debug-log` |
| Behavior | `-ac`, `-ach`, `-s`, `-v`, `-recursion`, `-recursion-depth`, `-config` |

## Path discovery

```shell-session
operator@lab:~$ ffuf -w approved-paths.txt -u https://app.example.test/FUZZ \
  -mc all -fc 404 -fs 127 -t 10 -rate 20 -of json -o evidence/paths.json
admin                  [Status: 403, Size: 226, Words: 18, Lines: 7]
api                    [Status: 301, Size: 169, Words: 5, Lines: 8]
health                 [Status: 200, Size: 31, Words: 2, Lines: 1]
```

## Parameters and virtual hosts

```bash
ffuf -w params.txt -u 'https://app.example.test/report?FUZZ=canary' -fs 842 -rate 10
ffuf -w hosts.txt -u https://192.0.2.10/ -H 'Host: FUZZ.example.test' -fs 127
```

Auto-calibration (`-ac`) sends baseline probes to derive filters; inspect its choices rather than trusting them blindly. Multi-wordlist modes can grow as a Cartesian product, so calculate request count before starting. Raw request files are best for complex authenticated requests; remove real secrets and preserve a test session only.

```text
wordlist A=5000, wordlist B=2000, clusterbomb requests=10,000,000
At 20 requests/second: approximately 5.8 days before retries
```

Use low rates, a finite runtime, and a canary request to verify authentication. Retain response samples for every distinct cluster rather than treating every matched line as a finding.

## Raw requests & multiple insertion points

Raw request files preserve methods, JSON bodies, custom headers, and cookies. Mark only approved positions with named keywords.

```http
POST /api/v1/search HTTP/1.1
Host: app.example.test
Content-Type: application/json
X-C2R-Test: authorized

{"field":"FIELD","value":"VALUE"}
```

```shell-session
operator@lab:~$ ffuf -request search.req -request-proto https \
  -w fields.txt:FIELD -w values.txt:VALUE -mode pitchfork \
  -mc all -fc 404 -rate 5 -maxtime 600 -of ejson -o evidence/search.ejson
id:canary              [Status: 200, Size: 412, Words: 18, Lines: 1]
```

Pitchfork pairs line 1 with line 1; Cluster Bomb tries every combination. Mismatched wordlist lengths in Pitchfork and accidental Cartesian growth are common operator errors.

## Recursion & calibration

Recursion can multiply traffic dramatically. Limit depth, require a status strategy compatible with redirects, and make each recursive discovery part of the approved URL scope. Use per-host calibration when multiple virtual hosts return different not-found pages.

## Crook2Root lab

Build a fixture with six response classes: stable 404, soft 404, redirect, authorization denial, rate limit, and server error. Derive filters from random controls, run at 1/5/20 requests per second, and document when `-ac` succeeds or masks a real route. The deliverable should include request count, matched/filtered counts, response samples, and a manual confirmation for every retained cluster.

## Failure analysis

Unexpected zero results often means an overbroad filter; millions of results usually means missing baseline calibration. Repeated timeouts can be rate limiting, proxy saturation, TLS handshake cost, or application queue pressure. Save debug logs and compare a single request with `curl` before changing multiple controls.

---
> 🔼 Up: [[Enumeration & Service Interaction Tools]]
