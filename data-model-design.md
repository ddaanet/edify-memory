---
name: data-model-design
description: "before writing a guard, a default or a fallback: check what the source guarantees — optionality, baked-in guarantees, which layer owns a rule, and code for cases that cannot occur"
metadata:
  node_type: memory
  type: feedback
---

Four rules that all fire at the same moment: a field, a guarantee, a guard or a
fallback is about to be written, and the data model has not been consulted.

## No code for impossible cases

Before handling a case, establish it can occur. Check the data model and the
upstream source; if they cannot produce the state, do not write code for it —
no guard, no fallback, no test.

The check is usually a cardinality argument. A comment carrying at most one
`parent_id` cannot produce a cycle reachable from a root — every member of a
cycle has its parent inside the cycle — so a walk that only descends from roots
cannot loop, and a `visited` set defends nothing. GitHub discussions nest
exactly one level, a reply cannot be replied to, so arbitrary-depth recursion
with an h6 clamp generalizes over a case the source cannot emit. In both, the
docstring already stated the constraint the code was written to violate.

**Why:** the cost is real (a NamedTuple to duck the arg-count lint the guard
caused, a second loop, a warning branch, tests) and the benefit is zero. Worse,
a passing test for an unreachable state reads as verification — it only proves
the code handles input the system cannot produce, so it makes dead machinery
look load-bearing and freezes it against later simplification.

**How to apply:** when my human partner gates a request on a condition
("... *if they exist*", "if that can happen"), that is an instruction to go
check, not a hypothetical to build for. Report what the check found. Contrast
with a case that genuinely occurs — an orphan reply whose parent is missing
from the corpus is real, because an incremental or paginated fetch can land a
reply before its parent; that one earns its handling.

## Optional means the source can omit it

Make a field optional (or give it a default) only where the upstream source can
genuinely omit it. Never optional "to be safe", "for robustness", or to make a
fixture easier to write.

**Why:** a defaulted field cannot distinguish *absent because the source said
so* from *absent because we stopped asking for it*. In a decode chain, the
second is a silent, whole-corpus data loss with no error anywhere — the struct
defaults to `None`, the next layer accepts the `None`, and the column is
quietly empty forever. Requiredness is what turns that into a loud failure at
the boundary that caused it. This is the impossible-cases rule above pointed at
schemas: check what the source actually guarantees rather than hedging.

**How to apply:** for each nullable-looking field, go read the upstream schema
before choosing. Required there → required here. Only genuine absence earns an
optional. Where a layer has a *different* answer from its neighbour, that
asymmetry is real information — write it down rather than flattening both to
optional.

Demonstrated on ghmem: `authorAssociation` was uniformly `str | None = None`
across the wire structs, the ledger contract, and the columns, though GraphQL
types it `CommentAuthorAssociation!` — non-null everywhere it appears. Dropping
it from the query passed every test and every layer, nulling the column
corpus-wide. Making it required closed it; making it required also
*immediately* caught seven fixture nodes that had already drifted without it.
`mergedBy`/`mergedAt` stayed optional, because an Issue node truly has neither
— the distinction the uniform-optional style had erased.

## Don't bake in a guarantee a later caller must relax

Don't design an API that is "buggy by design" by baking in a strong guarantee a
foreseeable caller will need to relax later. When a stronger promise
(durability, ordering, atomicity, validation) conflicts with a known future
need (throughput, batching), don't hardcode the strong version into every
operation — expose the guarantee as a separate opt-in method the caller invokes
at its own checkpoints.

**Why:** removing a guarantee from a published contract is a breaking change
and a design smell; adding an opt-in later is not. A weaker default + explicit
escalation is honest about what each call promises.

**How to apply:** concrete case — ghmem's `Ledger.append` used to fsync per
event, which would throttle Part 3's bulk fetch. Fix: `append` only flushes to
the OS (crash-visible), and a new `Ledger.sync()` delegates to `os.fsync` when
the caller wants power-loss durability.

## Shape rules are not duplication

When deciding whether an outer guard should reject something the inner layer
already rejects, the question is not "is this duplication?" but **"does the
rule need knowledge only the inner layer has?"**

If it does, leave it inside — two copies of a domain rule drift. If it does
not, it is a fact about the request's *shape*, and the guard is where it
belongs.

The call went backwards on ghmem's allowlist hook. Asked whether an empty
heredoc note should be legal, the argument was that the hook should stay
content-agnostic: it is a grammar, and which verdicts require a note lives in
`record_verdict` with both halves tested. My human partner overruled it — an
empty heredoc is malformed either way, because a verdict that takes a note has
none and one that takes no note should not have opened a heredoc at all.

That rule never has to know which verdict is in play, so it was never the
domain rule. The "don't duplicate" instinct had bundled two different rules
under one label and defended the wrong one.

**Why:** the layer that owns a rule is set by what the rule needs to decide,
not by which layer happens to catch it first. Get that wrong and a malformed
command is accepted by the guard, then accepted or rejected inconsistently
downstream depending on an unrelated field — here, `no-signal` would have taken
an empty note silently while every other verdict errored.

**How to apply:** before declining to add a check on duplication grounds, write
the rule out and see which layer's vocabulary it needs. "The heredoc must carry
something" needs none of the verdict vocabulary; "a topic verdict needs a note"
needs all of it. Put each where its vocabulary lives, and enforce the shape
rule at *both* boundaries when a caller can reach the inner layer another way —
the CLI got the same empty-note rejection so a non-hooked caller cannot do what
the hook forbids.

Related: [[scoring-formula-design]] for the same check-the-data instinct
applied to constants.
