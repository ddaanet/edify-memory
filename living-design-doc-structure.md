---
name: living-design-doc-structure
description: "User's required structure for a 'living design' doc — numbered FR, NFR, decisions, limitations, history"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8abf3f30-2f33-4c3c-92ba-59902ca0e65c
---

A **living design doc** (as opposed to a frozen spec or an executed plan) has a
fixed structure the user expects: **numbered FR** (functional requirements),
**NFR** (non-functional requirements), **decisions**, **limitations**, and
**history**.

**Why:** It is a requirements-tracking artifact, not prose — it aligns with
Edify's mission ([[verify-loop-direction]], Lean/formal requirements tracking).
Numbering makes requirements and limitations individually referenceable
(FR3, L5) and lets other artifacts cite them. It evolves in place; a frozen
spec/plan does not.

**How to apply:** When asked for a "live"/"living" design doc, do NOT reach for
the brainstorming skill or an AskUserQuestion about scope/format — the format is
settled. Write the doc with those five sections. Give it a stable (non-dated)
filename to signal it's living (frozen specs/plans are date-stamped). Each
FR/NFR row carries a state (Done / open); each decision row records what it
supersedes; each limitation carries a disposition. Place it as a sibling to its
origin spec/plan. Example:
`docs/superpowers/design/invariant-guided-verify-loop.md`.
