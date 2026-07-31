# Contributing to Crook2Root

Thank you for helping build Crook2Root into a rigorous, open cybersecurity curriculum. Contributions are welcome from learners, security engineers, researchers, educators, red teamers, blue teamers, platform specialists, and technical writers.

Before contributing, read the [Crook2Root Authoring Standard](docs/Crook2Root%20Authoring%20Standard.md). It defines the required zero-to-mastery structure, strict multiple-trees topology, visuals, evidence, naming, and completion criteria.

## Ways to Contribute

- Correct a technical error or outdated platform detail.
- Improve a beginner explanation without weakening expert depth.
- Add realistic output, troubleshooting, or a reproducible lab.
- Improve Mermaid architecture, sequence, state, or decision diagrams.
- Split a clumped subject into atomic leaves under the correct parent.
- Expand an intentionally scaffolded domain.
- Fix broken links, aliases, anchors, embeds, metadata, or curriculum order.
- Improve accessibility, clarity, consistency, and English prose.

## Repository Architecture

Crook2Root uses a strict hierarchy:

```text
Cyber Security
-> Domain Index
-> Branch or Sub-Index
-> Technical Leaf
```

Every node has one parent. Indexes provide vertical navigation. Technical leaves do not link laterally to other leaves because those edges turn the Obsidian Graph into an unreadable mesh.

Before adding a note, identify:

1. Its single parent.
2. Its domain folder and `tree/*` tag.
3. Its position among the parent’s direct children.
4. Whether it is truly atomic or duplicates an existing leaf.

## The Crook2Root Requirement

Every technical leaf must teach three levels:

- **Crook:** plain-language mental model, prerequisites, vocabulary, and first safe example.
- **Operator:** architecture, practical commands/code, realistic output, troubleshooting, and evidence.
- **Root:** internals, edge cases, failure modes, security consequences, debugging, and design tradeoffs.

Do not submit generic summaries, command dumps without interpretation, copied tool manuals, placeholder prose, or notes that assume expert prerequisites from the first paragraph.

## Required Learning Order

Every leaf repeats its parent’s first-degree curriculum near the beginning:

```markdown
## Parent Learning Order
First Leaf -> Second Leaf -> Third Leaf
```

The line must contain only direct sibling titles separated by `->`. Do not use wikilinks, bullets, numbering, descriptions, the parent title, or descendants. If you reorder a parent curriculum, update the order line in every direct child.

## Technical Leaf Template

```markdown
---
title: "Professional Topic Title"
aliases: ["Useful Historical Name"]
tags:
  - tree/domain
  - cyber/domain/topic
  - type/concept
  - level/operator
Domain:
  - "[[Single Parent]]"
Color: "#DOMAIN_COLOR"
---

# Topic Title

> [!abstract] Note of [[Single Parent]]
> State what the learner will understand and be able to do.

## Parent Learning Order
First Leaf -> This Leaf -> Final Leaf

## Start Here — Crook
Explain the mental model, vocabulary, prerequisites, and baseline.

## Architecture & Mechanics — Operator
Explain the real system and show practical evidence.

## Internals, Failure Modes & Security — Root
Teach implementation details, debugging, edge cases, and tradeoffs.

## Hands-On Lab
Provide prerequisites, procedure, expected output, interpretation, and cleanup.

## Crook → Operator → Root Checkpoint
Test understanding at all three levels.

---
> 🔼 Up: [[Single Parent]]
```

Section names can change to suit the subject, but every function represented above must remain.

## Visuals & Assets

- Prefer Mermaid for flows, states, sequences, architecture, and trust boundaries.
- Use SVG, PNG, or GIF assets when they add information Mermaid cannot express.
- Store assets under `assets/` and embed them with `![[asset.ext]]`.
- Explain how to read every visual.
- Do not add decorative diagrams that merely duplicate nearby prose.
- Preserve existing embeds and verify that every asset resolves.

## Practical Examples

Examples should include exact commands, requests, code, configuration, or debugger interaction plus realistic expected output. Explain which fields matter and how to distinguish success from a misleading result.

Labs must be:

- Authorized and bounded.
- Reproducible on a disposable or clearly defined environment.
- Safe for the stated objective.
- Explicit about prerequisites and scope.
- Equipped with cleanup instructions when state changes.

Use synthetic domains, hosts, addresses, users, records, and markers. Do not include secrets or proprietary customer data.

## Naming & Style

- Write in professional English.
- Use clean filenames without numeric prefixes, underscores, or synthetic module codes.
- Do not prefix notes with `Hub` or `Branch`.
- Use `&` instead of “and” in visible titles joining peer concepts.
- Define acronyms when first introduced.
- Prefer precise explanations over jargon or marketing language.
- Keep a topic atomic; split genuinely independent subjects.
- Mention related leaves in **bold**, not `[[wikilinks]]`.

## Metadata & Graph Rules

- Preserve or add exactly one `Domain:` parent.
- Use the domain’s existing `Color:` value.
- Include the appropriate `tree/*` tag so Graph View coloring works.
- Add aliases when renaming or replacing a historical note.
- Parent indexes may link to direct children; leaves may link only to their parent.
- Do not create shortcuts between domain hubs or sibling leaves.

## Contribution Workflow

1. Search existing notes and open issues before starting.
2. Choose one focused change or coherent cluster.
3. Fork the repository and create a clearly named working branch.
4. Make the smallest architectural change that fully solves the problem.
5. Perform the validation checklist below.
6. Open a pull request explaining the parent, learning position, technical scope, lab environment, and validation results.
7. Respond to review with evidence and update every affected sibling order if curriculum placement changes.

## Validation Checklist

- [ ] The note starts at true beginner level and reaches expert depth.
- [ ] `Parent Learning Order` matches the parent’s direct children exactly.
- [ ] The note has one parent and no lateral leaf wikilinks.
- [ ] Domain tag and color match the parent tree.
- [ ] Commands/code have realistic expected output and interpretation.
- [ ] At least one meaningful visual is present.
- [ ] The lab is authorized, reproducible, and cleaned up where necessary.
- [ ] The Crook → Operator → Root checkpoint is complete.
- [ ] Frontmatter and Markdown fences are valid.
- [ ] Wikilinks, aliases, anchors, and image embeds resolve.
- [ ] No placeholders, secrets, customer data, or unrelated generated text remain.

## Ethical & Responsible Contributions

Crook2Root documents security mechanisms and authorized testing so systems can be understood, evaluated, and improved. Contributions must not claim authorization where none exists, target real third parties, expose private data, or present destructive behavior without a legitimate educational and defensive context.

If a contribution describes offensive behavior, include scope, prerequisites, bounded validation, operational impact, and appropriate safeguards. Clearly distinguish demonstrated behavior from hypothetical escalation.

## Pull Request Description

A strong pull request answers:

- What did you change?
- Which parent owns the note?
- Where does it belong in the learning order?
- What new beginner, operator, and root-level value does it add?
- How were commands, output, diagrams, links, and labs verified?
- Did any filenames, aliases, parent links, or sibling order lines change?

Focused, technically justified contributions are easier to review and more valuable than large unstructured rewrites.

