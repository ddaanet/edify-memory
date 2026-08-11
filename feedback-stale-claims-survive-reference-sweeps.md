---
name: feedback-stale-claims-survive-reference-sweeps
description: "Rewiring a document's references does not validate its claims — after a platform change, re-derive the factual assertions, don't just repoint the paths"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-10T22:40:22.485Z
---

A reference sweep fixes paths and command names. It does not touch the
*assertions* built on top of them, which fail silently and keep their authority.

**Why:** During [[workflow-pipeline-revival]] I rewired `agents/decisions/*.md`
onto live components, then reasoned from those same documents as authoritative.
Two capability claims in `project-config.md` (2026-02-15) turned out false when
finally measured — subagents do get the memory index, and they do have the
`Skill` tool ([[cc-subagent-context-capabilities]]). Both were true of the
system that had been torn down. I had repeated the first one to the user as
settled fact; it took two rounds of "are you sure?" to get it checked.

**How to apply:** When a document survives a platform or architecture change,
separate its two layers. References get mechanically repointed. Claims about
*what the platform does* get re-derived against the current platform before
being used as premises — especially claims about capability boundaries ("X
cannot do Y"), which are the ones a vendor most often relaxes and which nothing
in the codebase will contradict. A cheap empirical probe beats an inherited
assertion: for agent-context questions, dispatch a subagent under a zero-tool
constraint and have it quote verbatim content it could not fabricate.

Related: [[feedback-decision-docs-are-living]].
