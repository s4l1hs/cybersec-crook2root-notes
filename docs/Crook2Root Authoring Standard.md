# Crook2Root Authoring Standard

Crook2Root is a cybersecurity knowledge system designed to take a reader from complete beginner to independent expert. The name is an editorial promise: every technical leaf must be understandable at **Crook**, usable at **Operator**, and defensible at **Root**.

This document is the permanent content philosophy for every domain. Contributors and automated editors should apply it without requiring the project owner to repeat it in each request.

## 1. The Three-Level Learning Contract

### Crook — Build the Mental Model

Assume the learner has never seen the subject.

- Explain what the thing is and why it exists.
- Define prerequisites and every essential term before using it heavily.
- Use a concrete analogy, then state where the analogy stops being accurate.
- Show the smallest safe example and its expected result.
- Identify the object, protocol, process, or trust boundary being studied.
- Avoid unexplained acronyms, cargo-cult commands, and premature edge cases.

A Crook section succeeds when a new learner can describe the mechanism in plain language and reproduce the baseline example.

### Operator — Make It Work & Diagnose It

Move from recognition to reliable operation.

- Explain architecture, data flow, state transitions, inputs, outputs, and dependencies.
- Show realistic commands, configuration, code, requests, responses, logs, or debugger output.
- Explain every important flag or field used in the example.
- Include normal output, at least one failure mode, and a troubleshooting workflow.
- Teach evidence collection: what proves the conclusion and what could produce a false conclusion?
- Use a bounded hands-on lab with prerequisites, procedure, expected output, interpretation, and cleanup.

An Operator section succeeds when the learner can investigate an unfamiliar system without copying a memorized recipe blindly.

### Root — Explain the Internals & Tradeoffs

Reach implementation-level understanding.

- Trace the mechanism through parsers, protocols, runtimes, kernel structures, cryptographic assumptions, or control planes as appropriate.
- Cover edge cases, version or platform differences, race conditions, resource limits, failure recovery, and security boundaries.
- Explain how controls fail, how to debug the failure, and how design choices change risk.
- Distinguish demonstrated facts, inferences, and hypotheses.
- Connect offense, defense, engineering, and forensic evidence under authorized use.
- End with a **Crook → Operator → Root** checkpoint that tests explanation, operation, and independent reasoning.

A Root section succeeds when the learner can predict behavior, design a defensible experiment, and explain the result from first principles.

## 2. Required Leaf Anatomy

Section names may adapt to the subject, but every technical leaf needs the following functions:

1. YAML frontmatter with one parent, domain tag, level/type tags, aliases, and matching color.
2. A clear H1 and a short abstract stating the learning outcome.
3. `## Parent Learning Order` followed by one plain-text order line.
4. Beginner mental model and prerequisites.
5. Architecture or mechanics explanation.
6. At least one meaningful visual.
7. Practical commands, code, or protocol examples with realistic output.
8. Failure modes and troubleshooting.
9. Security implications and authorized-use boundaries.
10. A reproducible lab or exercise.
11. A Crook → Operator → Root checkpoint.
12. A single parent link at the footer.

Recommended technical-leaf depth is approximately 1,500–5,000 words. Complexity—not padding—determines the final size. Shorter is acceptable only when the topic is genuinely narrow and still satisfies the complete learning contract.

## 3. Parent Learning Order Rule

Each parent owns an ordered curriculum of its direct children. Every direct child repeats that same first-degree order near the beginning:

```markdown
## Parent Learning Order
Foundations -> Architecture -> Operation -> Troubleshooting -> Mastery
```

Rules:

- Use `->` exactly as the separator.
- List direct siblings only.
- Do not include grandchildren or deeper descendants.
- Do not add descriptions, numbers, bullets, or commentary to the order line.
- Do not use wikilinks on the order line; lateral links would collapse the strict tree topology.
- Use the professional visible title of each sibling.
- Update every sibling if the parent curriculum changes.

## 4. Visual Standard

A visual must teach a relationship that prose alone would make harder to understand.

Use Mermaid for:

- `sequenceDiagram` — protocol exchanges, syscalls, authentication, remote execution, and request lifecycles.
- `flowchart` — architecture, decision paths, trust boundaries, and transformations.
- `stateDiagram-v2` — process, protocol, scheduler, or resource states.
- `classDiagram` — object ownership and structural relationships.
- `timeline` — boot, incident, or forensic sequences.

Use an image or animation when motion, physical layout, UI state, packet timing, or hardware arrangement cannot be represented clearly in Mermaid. Store repository assets in `assets/` and embed them with `![[asset.ext]]`. Every visual needs nearby prose explaining how to read it and why it matters.

Decorative diagrams, repeated generic flows, and visuals that merely restate a list do not satisfy the standard.

## 5. Practical Evidence Standard

Commands without output teach syntax but not interpretation. Every important workflow should show:

- The exact command, request, code, or configuration.
- A realistic expected result.
- Which fields matter and why.
- One likely error or misleading outcome.
- A safe troubleshooting step.
- Scope and cleanup when the exercise changes state.

Use synthetic hosts, identities, domains, addresses, records, and markers. Authorized offensive material should establish the mechanism with bounded evidence and explicit stop conditions.

## 6. Strict Multiple-Trees Architecture

The graph is intentionally hierarchical:

```text
Cyber Security
-> Domain
-> Branch or Sub-Index
-> Technical Leaf
```

- A node has one parent.
- Root and indexes create vertical edges.
- Leaves do not link laterally to other leaves.
- Cross-topic references use **bold text**, not wikilinks.
- `Domain:` creates the child-to-parent graph edge.
- Domain `tree/*` tags drive Graph View colors.
- A concept needing substantial independent treatment becomes an atomic leaf under the correct parent rather than a large lateral section.

## 7. Naming & Metadata

Use professional names that resemble an enterprise wiki:

- No numeric folder prefixes.
- No underscores.
- No synthetic module codes such as `LNX.1`.
- No `Hub` or `Branch` prefix.
- Use `&` instead of “and” in visible titles that join peer concepts.
- Keep filenames stable unless a rename materially improves the architecture.
- Preserve useful aliases so historical names continue resolving.

Example:

```yaml
---
title: "Windows Memory Internals & Exploit Mitigations"
aliases: ["Windows Memory", "VAD", "Windows Mitigations"]
tags:
  - tree/os
  - cyber/foundations/windows
  - type/concept
  - level/root
Domain:
  - "[[Windows]]"
Color: "#FFA500"
---
```

## 8. Editorial Quality

- Write in professional English.
- Prefer precise plain language over inflated jargon.
- Define acronyms on first use.
- Separate stable principles from version-specific behavior.
- Mark simulations, illustrative output, assumptions, and inferences honestly.
- Do not use generic filler or mechanically repeat paragraphs across notes.
- Replace platform branding from training providers with technology-focused instruction.
- Preserve existing image embeds and user-authored material unless the task explicitly supersedes it.

## 9. Definition of Done

A leaf is complete only when:

- A beginner can enter without an unstated prerequisite.
- An operator can reproduce and troubleshoot the workflow.
- An expert can reason about internals, edge cases, security, and evidence.
- The first-degree learning order matches the parent.
- The parent link, Domain property, color, aliases, tags, code fences, visuals, and embeds are valid.
- No sibling or lateral leaf wikilinks were introduced.
- Commands include realistic output and interpretation.
- The lab is bounded, authorized, and reproducible.
- No generic placeholders remain.

