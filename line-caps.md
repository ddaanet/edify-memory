---
name: line-caps
description: "the 400-line caps in this repo: `scripts/check_line_limits.sh` is a hard gate on `src`/`tests`, the plan/PR cap is soft; a file parked at exactly 400 has stopped signalling, and a ~400-line contract predicts a ~1400-line implementation"
metadata:
  node_type: memory
  type: feedback
---

Two different 400-line caps apply here, and they behave differently.

**`scripts/check_line_limits.sh` is a hard gate** over `src` and `tests`, wired
into the `precommit`/`release` recipes ([[python-standards]]). Going over it
breaks the build.

**The 400-line cap on human-reviewed artifacts** — plans, PRs, specs — is a
**soft** guideline for reviewability. Don't block a commit or agonize over
exceeding it in local self-review.

## What to do when a source file goes over

Split the module along an existing seam. Do not shave the docstrings to fit.

Fixing ghmem's `status` two-population bug pushed `store.py` from 389 to 406,
and trimming the two new docstrings brought it back to exactly 400 — with the
`heat_query.py` split left undecided on the open-decisions list. Trimming *was*
the conservative call there, but it is borrowing against the split, not an
alternative to it. Prose is the cheapest thing in the file to cut and the first
thing a later reader needs, and a file parked at exactly its cap has stopped
emitting the signal the cap exists to emit — the next line added anywhere
breaks the build, which reads as a tooling failure rather than as "this module
is due to be split". If trimming is the only way to fit, say so out loud and
put the split in front of the human as a decision; never let the cap be
satisfied quietly by prose loss.

An uncram pass ([[style-edit-briefs]]) legitimately pushes a file over. Split
it; don't re-cram.

## What to do when a plan or PR goes over

Hard-wrap prose at the repo's ~72-col convention first, THEN measure. Never
keep the count down by cramming content onto 150–300-char single lines — long
unwrapped lines game the number while destroying the reviewability the cap
protects. Split on a clean seam if still over, preserving all information:
duplicated front matter (constraints, shared types) is fine; dropped content is
not. GitHub bodies are the opposite case — they render every newline as a
break, so no hard wrap there.

**Splitting is not free, so this cap is not absolute.** It is a *reader-context*
budget, and a split imposes its own reader cost: re-reading several docs,
chasing cross-references between them. When that cost would exceed what the
overage saves, accept a bounded overage on one cohesive doc. The honest-wrap
discipline still holds; only the "always split when over" reflex is tempered.

## Contract size predicts implementation size

When a single spec/plan task's **contract** runs past ~400 lines, its
implementation almost certainly overshoots the 400-line PR cap too. Observed at
roughly 3.5× — a ~400-line contract producing a 1385-line implementation
(ghmem, 2026-07-17). Catching the overshoot at spec-review time is cheap;
discovering it at PR-slicing time is expensive re-work, so treat a task's
contract approaching 400 lines as the signal to **split that task**.

Related: [[review-slices]] for re-expressing an oversized change as slices;
[[genuine-red-not-missing-sut]] for the one-contract-per-line rule that keeps
an interface from being crammed into a run-on paragraph.
