---
title: "YARA"
aliases: ["YARA Rules"]
tags: [tree/tooling, cyber/tooling/defensive/content-analysis/yara, level/master]
Domain: "[[Malware & Content Analysis Tools]]"
Color: "#708090"
---

# YARA

YARA classifies files, memory images, and byte streams using strings, structural conditions, metadata, and modules. It should identify a meaningful family or behavior—not merely one common byte sequence.

```mermaid
flowchart LR
    O["Preserved object"] --> R["Compiled YARA rules"]
    R --> S["String & module scan"]
    S --> C["Condition evaluation"]
    C --> M["Match metadata"]
    M --> T["Analyst triage"]
```

## Installation & CLI

```shell-session
analyst@lab:~$ sudo apt install yara
analyst@lab:~$ yara --version
4.x
```

Common options include `-r` recursive, `-f` fast scan, `-w` suppress warnings, `-s` print matching strings, `-m` metadata, `-g` tags, `-n` negate result, `-t tag`, `-i identifier`, `-x module=data`, `-d name=value` external variable, `-p` threads, `-a` timeout, `-C` compiled rules, and `--max-rules`/stack controls depending on version. `yarac` compiles rule sets for validation and faster distribution.

## High-quality rule example

```yara
rule C2R_Training_Document_Marker : training document
{
  meta:
    author = "Detection Engineering"
    purpose = "Authorized canary validation"
    version = "1.0"
  strings:
    $marker = "C2R-TRAINING-DOCUMENT" ascii wide
    $xml = "[Content_Types].xml" ascii
    $macro = "vbaProject.bin" ascii
  condition:
    filesize < 20MB and $marker and $xml and $macro
}
```

```shell-session
analyst@lab:~$ yarac rules/c2r-training.yar build/c2r-training.yarc
analyst@lab:~$ yara -C -s -m build/c2r-training.yarc samples/canary.docm
C2R_Training_Document_Marker [author="Detection Engineering",purpose="Authorized canary validation"] samples/canary.docm
0x18f2:$marker: C2R-TRAINING-DOCUMENT
```

## String & condition mastery

Strings may be text, hexadecimal patterns with wildcards/jumps/alternatives, or regular expressions. Modifiers include `ascii`, `wide`, `nocase`, `fullword`, `xor`, and `base64` variants where supported. Conditions can use counts (`#a`), offsets (`@a[1]`), lengths (`!a[1]`), ranges, `for any/all`, file size, entry point, and modules such as PE, ELF, hash, math, dotnet, or time.

Avoid fragile rules built from packer stubs, compiler artifacts, or one public string. Select multiple independent features, use structural checks, and test against a large benign corpus. Measure precision, recall, scan time, and maximum object size. Rules that time out or cause excessive endpoint CPU are operational defects.

## Deployment

Sign/version bundles, compile in CI, validate syntax, test positives/near-misses/benign corpora, and record rule provenance. Preserve original sample hashes and do not execute unknown objects. A YARA match is a triage classification, not automatic proof of maliciousness.

## Rule development methodology

Begin with labeled samples and a hypothesis about stable family or behavior features. Extract candidate strings, structure, imports, sections, constants, and metadata. Remove environment-specific/common-library artifacts, then combine independent features.

```yara
$prologue = { 55 48 89 E5 48 83 EC ?? }
$variant  = { 48 8D 05 [4-12] ( 48 89 C7 | 48 89 C6 ) E8 ?? ?? ?? ?? }
```

Hex patterns support wildcards, nibble wildcards, alternatives, and bounded jumps. Wide, nocase, XOR, Base64, and regex matching can expand work dramatically; profile them against large benign objects.

## Format modules

The PE module exposes headers, imports, exports, resources, sections, signatures, and timestamps. ELF, Mach-O, .NET, hash, math, and other modules add format-aware predicates when available. Structural checks are usually more stable than raw offsets.

## Mastery lab

Use five authorized positives, five near variants, and at least 10,000 benign files. Compile a tagged rule, measure speed, investigate every false positive, and preserve a regression corpus.

```shell-session
analyst@lab:~$ time yara -r -p 4 rules/c2r-family.yar benign-corpus/ > benign.matches
real    0m8.421s
analyst@lab:~$ wc -l benign.matches
0 benign.matches
analyst@lab:~$ yara -s rules/c2r-family.yar positive-corpus/
C2R_Family positive-corpus/sample-03.bin
0x41a:$config_marker: C2R-CONFIG-V2
```

Too many matches means weak features; no matches suggests encoding, packing, offsets, module availability, or filesize guards. Slow scans require reducing expensive regex/modifiers, bounding filesize, compiling rules, and profiling corpora.

---
> 🔼 Up: [[Malware & Content Analysis Tools]]
