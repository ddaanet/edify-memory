---
name: feedback-decision-docs-are-living
description: agents/decisions/*.md are living design documents — rewire their references when components change; never treat them as immutable ADRs
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-10T22:40:15.800Z
---

`agents/decisions/*.md` in edify are **essential living design documents**, not
archival ADRs. When the components they reference change, **rewire the
references**. Do not leave them verbatim, and do not drop the documents.

**Why:** I restored these docs during [[workflow-pipeline-revival]], noticed the
mechanical rewiring pass had rewritten references inside them, and reverted it —
reasoning that an ADR records what was decided *when*, so rewriting it to match
current tooling falsifies the record. I reported that as a deliberate judgment
call. The user corrected it: these documents carry institutional memory, and
leaving them pointing at components that no longer exist degrades them. The
"historical record" framing was mine, not the project's.

**How to apply:** When a component is removed or replaced, sweep
`agents/decisions/` alongside the code. Substitute the live successor where one
exists (`/plan-adhoc` → `/runbook`, `memory-index.md` → `memory/MEMORY.md`).
Where the mechanism died but the *principle* survives, generalize the prose
rather than deleting the decision — "`edify _recall resolve` is the canonical
gate anchor" becomes "the recall pass is the canonical gate anchor". Keep a
reference only where the decision is *about* the dead thing and naming it is the
point (e.g. evidence that precommit broke for 9 days on a non-existent `edify
validate` command). Where a script simply was not restored, say so inline rather
than silently pointing at a missing path.
