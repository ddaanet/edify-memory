---
name: feedback-decision-docs-are-living
description: "`docs/design.md`, folded from 23 `agents/decisions/` ADR files 2026-08-14; where its scope ends against `docs/superpowers/`, and L-5's retirement"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-09-02T13:02:55.575Z
---

edify's architectural record is a **single living design document**,
`docs/design.md`. `agents/decisions/` held it as 23 dated ADR files until
2026-08-14; the content was folded into the one doc, and content describing
torn-down subsystems was dropped rather than carried across.

**Why:** the framing those files came from — that a decision record captures
what was decided *when*, so rewriting it to match current tooling falsifies the
record — was judged wrong for this project. Git history is the audit trail; the
document is current truth.

**How to apply:** the general rules live in the tier —
[[ddaanet/design-doc-writing]] for structure and the write-time-record split,
[[ddaanet/design-doc-rewire-dead-components]] for a reference whose component
died, [[ddaanet/design-doc-no-situational-state]] for a figure that goes stale.
What is edify's own:

- **L-5 retired 2026-09-02.** It recorded `memory/MEMORY.md`'s byte count
  against the loader cutoff and went stale. `§1 Now` and the `§7`
  rejected-alternative entry keep the mechanism and name `/gitlore:index-audit`
  as the pass that acts on it. The id gap stands.
- **Scope boundary.** `docs/design.md` is the project-level record for
  edify-shaped work. `docs/superpowers/` is not a fossil of the torn-down
  superpowers subsystem — it holds superpowers-shaped docs, including a live
  subproject design with its own FR/NFR lifecycle. Do not propose renaming or
  folding it on the assumption that the name is dead.
