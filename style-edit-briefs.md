---
name: style-edit-briefs
description: "the uncram pass — crammed prose has three tells (multi-rule blocks, semicolon chains, colon-joined fragments), keep load-bearing em-dashes, and a goal-only brief gets applied as find-and-replace"
metadata:
  node_type: memory
  type: feedback
---

## The three tells of crammed prose

Prose written under a line cap crams in three recurring shapes: a docstring
stating four or five separate rules in one unbroken block; contract
documentation run together as a semicolon chain (`Keys: id; itemId
(**injected**: ...); author; ...`); and rationale telegraphed down to clause
fragments joined by colons. An "uncram pass" unpacks these — one idea per
paragraph, one annotated key per line — adding and dropping nothing.

**Why:** density under pressure is not concision. Every rule, exception and
reason survives the pass; only the packing changes. My human partner asks for
this as a distinct pass ("do the uncram pass on X"), separate from any
behaviour change.

**How to apply:** split genuine pile-ups only. A single em-dash or an
explanatory colon is load-bearing — `Not its own table — nested JSON inside
...` says more, in less, than two flat sentences, so flattening it trades terse
for limp and is a regression. Context paragraphs go *before* a test docstring's
`When:`/`Then:` block, never after. If the pass pushes a file past its line
cap, split the module along an existing seam rather than shaving the prose back
to fit — see [[line-caps]].

## A style brief that states only the goal gets find-and-replace

Editing comments and docstrings for *style* delegates badly when the brief
states a goal without a method. Given a brief saying "split em-dash pile-ups"
and "unpack telegraphic fragments", subagents systematically converted every
single em-dash and every explanatory colon into a full stop, producing limp
replacements ("Not its own table — nested JSON inside…" became "This is not its
own table. It is nested JSON inside…"). A corrective wave was worse: it left
files whose exact regressions had been quoted to it untouched, over-reverted
others back to zero, and raced the main session's own edits until it was
stopped.

**Why:** the brief said *what* to do but never *how* to unpack a run-on
paragraph, and an underspecified style rule affords only find-and-replace. The
judgement it actually requires — *is this edit clearer, or just longer?* — was
never briefed, so it was never applied. Terse voice reads as "crammed" to an
agent told to uncram.

**Two limits on this evidence, both load-bearing.** "Limp" and "flat" were the
editing agent's *own* verdicts on the replacements in that session; my human
partner never confirmed a regression, so this is a self-assessment, not a
measured quality drop. And the cause was brief specification, not model tier —
nothing here says a cheaper model would have done worse against a *well*-
specified brief. Do not cite this memory as a prior that prose degrades
down-tier; it does not support that claim.

**How to apply:** brief the *how*, not just the what — a worked before/after
example of an unpacking, and an explicit "keep the em-dash, split the ideas"
beats any amount of restating the goal. Where that cannot be written down,
delegate the mechanical, verifiable half (semicolon key-chains into bullet
lists; splitting a genuinely multi-idea paragraph) and keep the rest.
Structural work with a machine-checkable exit condition delegates fine — the
`raw_nodes.py` split with "`check_line_limits.sh` must exit 0" came back
correct on the first try. Whatever comes back, read the full diff before
reporting it as done; "agent reported success" is not verification. See
[[green-is-not-evidence]] for the same evidence-over-assertion instinct.
