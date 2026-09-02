---
name: review-slices
description: "re-expressing a big change as reviewable slices: build forward from the final state for landed history, work index-only for an uncommitted tree — cherry-pick reordering and `git checkout --` both fail"
metadata:
  node_type: memory
  type: feedback
---

Two different problems, and the method differs by whether the final state is
already safe in git.

## Landed history: build each slice forward, never cherry-pick

When a feature landed as many interleaved commits and needs re-expressing as a
few reviewable slices, do NOT cherry-pick the commits into slice order. It
conflicts immediately and everywhere: later commits rewrite earlier ones
wholesale (ghmem's `heat.py` was created once and then replaced by the
aggregate-query rewrite), and the files every slice shares — models, store,
CLI, the ingest reducer — are touched by every group.

Instead build each slice's tree **forward** from the final state:

1. Branch off the base. `git checkout <final> -- <this slice's files>`.
2. Hand-strip whatever belongs to LATER slices out of the shared files (delete
   the entity classes, the query methods, the CLI command).
3. Run the full gate. Commit. Branch again for the next slice, stacked.

Stacking is what makes the shared files tractable — each slice's diff is
against its parent, so the shared file grows by accretion instead of needing an
independent split per branch.

Two checks that make the result trustworthy:

- **Every slice green on its own**, or the slicing is worthless.
- **The tip's tree byte-identical to the original**:
  `git diff --stat <old-tip> <new-tip>` must be empty. That is the whole
  correctness claim — the rewrite changed how the work is told, not what it is.

Ordering falls out of dependencies, not of history. And **fold bug-fix commits
into the feature they fix**: a reviewer reading a defect followed by its repair
learns nothing the final state does not show, and the reasoning lives in the
docstrings anyway.

**Why:** slicing ghmem's Part A (21 commits, 5 shared files) into 6 commits.
The cherry-pick attempt conflicted on the second commit; the forward
construction took one pass per slice with no conflicts at all.

**How to apply:** expect the slice count and boundaries to change once you
build them — a diffstat predicts size, not coherence. Part A's routing slice
looked like one 662-line batch and turned out to be two distinct arguments
(what shape routing state is / how a live write avoids diverging from a replay)
that read better apart.

## Uncommitted tree: stage each slice into the index, never touch the tree

Slicing a big **uncommitted** working tree into commits is not the same
problem. There the final state is safe in git; here it exists in exactly one
place, so the "reset to base and rebuild forward" move is unavailable —
`git checkout -- <dir>` is blocked by the permission classifier as destructive,
and rightly so.

Work **index-only** instead. The working tree stays at the final state from
first commit to last:

- Whole-file slices: `git add <paths>`, commit, repeat.
- A file spanning two slices: write the intermediate content to the scratchpad,
  then `blob=$(git hash-object -w <file>)` and
  `git update-index --cacheinfo 100644,$blob,<repo-relative-path>`. That stages
  exact bytes without a hand-counted `git apply` hunk split, which is where
  this otherwise goes wrong.
- Last slice: `git add -A <dir>` sweeps the remainder.

**Why:** the post-B2 ghmem wave — 706 insertions across 25 files, six slices.
Only two files needed splitting, and both were one Edit on a scratchpad copy
rather than a patch surgery.

**How to apply:** tar the tree to the scratchpad first and `diff -r` the
extract against it, so the backup is *proven* before any staging starts.
Afterwards, the same `diff -r` against HEAD is the correctness claim — the
slicing changed how the work is told, not what it is.

Intermediate commits cannot be gate-tested in place, since the tree is never at
an intermediate state. Verify after the fact with
`git worktree add --detach <sha>`, running the main tree's venv against it:
`(cd <wt>/tools/ghmem && PYTHONPATH=<wt>/tools/ghmem/src
<main>/.venv/bin/python -m pytest)`. PYTHONPATH wins over the editable install,
so the worktree's own sources are what get imported.

Related: [[line-caps]] for what a slice's size is being measured against.
