---
name: feedback-decision-docs-are-living
description: edify's design record is one living doc (docs/design.md) — rewire its claims when components change; never freeze it as an ADR archive
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-14T18:06:21.787Z
---

edify's architectural record is a **single living design document**,
`docs/design.md`, not an archive of dated ADRs. When the components it
references change, **rewire the document**. Do not leave stale references
standing, and do not drop the decision.

**Why:** the earlier framing — that a decision record captures what was decided
*when*, so rewriting it to match current tooling falsifies the record — is
wrong for this project. These documents carry institutional memory, and leaving
them pointing at components that no longer exist degrades them. The audit trail
lives in git history; the document is current truth.

**How to apply:** when a component is removed or replaced, sweep the design doc
alongside the code. Substitute the live successor where one exists. Where the
mechanism died but the *principle* survives, generalize the prose rather than
deleting the decision — "`edify _recall resolve` is the canonical gate anchor"
becomes "the recall pass is the canonical gate anchor". Keep a reference to a
dead component only where the decision is *about* that component and naming it
is the point (e.g. evidence that precommit broke for 9 days on a non-existent
`edify validate` command). Where a script simply was not restored, say so
inline rather than silently pointing at a missing path.

`agents/decisions/` held this record as 23 separate files until 2026-08-14; the
content lives in `docs/design.md`, and content describing torn-down subsystems
was dropped rather than carried across. Related: [[ddaanet/design-doc-writing]].

**Scope boundary.** `docs/design.md` is the project-level record for
edify-shaped work. `docs/superpowers/` is not a fossil of the torn-down
superpowers subsystem — it holds superpowers-shaped docs, including a live
subproject design with its own FR/NFR lifecycle. Do not propose renaming or
folding it on the assumption that the name is dead.
