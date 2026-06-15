---
name: living-design-doc-structure
description: "User's required structure for a 'living design' doc — numbered FR, NFR, decisions, limitations, history"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8abf3f30-2f33-4c3c-92ba-59902ca0e65c
---

A **living design doc** (as opposed to a frozen spec or an executed plan) is the
user's standard cross-session context-preservation artifact, used everywhere.
Core sections: **numbered FR** (functional requirements), **NFR**, **decisions**,
**limitations**, **history**. Refined 2026-06-15 to optimize for *resuming*, not
just recording state — full section list below.

**Why:** It is a requirements-tracking artifact, not prose — it aligns with
Edify's mission ([[verify-loop-direction]], Lean/formal requirements tracking).
Numbering makes requirements/limitations individually referenceable (FR3, L5)
so other artifacts can cite them. It evolves in place; a frozen spec/plan does
not. The 2026-06-15 additions all target the same failure: a snapshot tells a
cold next-session reader *what is* but not *what's live, where the code is, or
how much to trust the "Done"s*.

**Full section set (in order):**
1. **Status + `Verified against: <commit> (<date>)`** stamp — anchors every
   state claim so a reader knows what to `git diff` for drift. Point-in-time
   "Done" rots otherwise.
2. **Now** — the resume pointer: Focus / Next / Do-not (re-litigated decisions).
   The single highest-value section; without it the next action is only
   inferrable from limitations + history.
3. **Status legend** — fixed vocabulary (`Done` = impl + pinned by test;
   `Done (prose)` = skill instructions, not code/tested; `Partial`; `Planned`).
4. **FR / NFR tables** with a **`Where · pinned by`** column mapping each to its
   code symbol + the test that proves it. Turns the doc into a navigation index
   and makes "Done" re-checkable, not a bare claim. *Verify symbols/tests exist
   before writing them — never assert an unverified pin.*
5. **Decisions** — each row: rationale + **`Supersedes / Reopen-if`** (the
   condition that puts a settled decision back on the table; stops
   re-litigation and flags when a parked call is live again).
6. **Limitations** (inherent — *can't*) split from **Non-goals** (deliberate —
   *won't, for now*). Conflating them hides whether an exclusion is a hard wall
   or revisitable scope.
7. **History** — design-significant events only (decisions reversed,
   requirements added/dropped). NOT an edit changelog, or it bloats.

**How to apply:** When asked for a "live"/"living" design doc, do NOT reach for
the brainstorming skill or an AskUserQuestion about scope/format — the format is
settled. Stable (non-dated) filename to signal it's living (frozen specs/plans
are date-stamped). Sibling to its origin spec/plan. Reference example with all
sections: `docs/superpowers/design/invariant-guided-verify-loop.md`.
