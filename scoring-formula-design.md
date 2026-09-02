---
name: scoring-formula-design
description: "choosing a weight, threshold or scoring constant: ground it in the corpus, bind it as a parameter, and judge it conditionally not marginally"
metadata:
  node_type: memory
  type: feedback
---

Three rules govern the moment a score, weight or threshold gets a number.

## Ground the constants in the data

Never ship hip-shot heuristic constants. Any scoring formula, weight, or
threshold must be grounded in analysis of the actual data it will run on, with
the analysis recorded.

**Why:** a constant chosen by intuition encodes a guess as a specification, and
nothing downstream distinguishes it from a measured one. A plausible-looking
weight (`comments + reactions + 2*participants`) and a round threshold (3 PRs,
10 commits) survive review precisely because they look deliberate.

**How to apply:** before fixing constants, run a cheap distribution analysis
over the real corpus (percentiles, known-ground-truth calibration — e.g. do
known maintainers classify as maintainers, do known-hot threads rank hot?),
record it, and derive the constants from it. Keep them tunable, but tuned from
evidence.

## Uncalibrated values do not licence placeholder code

A weight or threshold still awaiting calibration does not justify a placeholder
*implementation*. Weights are parameters: bind them into the query. Only a
change in the model's SHAPE (nonlinear score, rank fusion, a normalization
pass) would force a rewrite — "the numbers aren't final" never does.

Corollary: a per-item Python loop calling one query method per metric is a
reimplemented `GROUP BY`. Check whether the aggregate is expressible in SQL
before writing the loop.

Second corollary: if the consumer of some output is analysis, emit a data
format (CSV with a header, JSONL) — not a dense human line that has to be
regex'd apart. Truncating expensive noise with a `--limit` is not a fix; it
yields less noise while breaking the one consumer that needs the whole
distribution.

**Why:** my human partner caught `ghmem heat` fanning out ~5 queries per item
over a ~3.8k-item corpus to print one bespoke line each, with no programmatic
consumer — and rejected both the "calibrate first, then optimize" defence and
the `--limit` half-measure. The rewrite was one correlated-subquery aggregate
with the weights bound as parameters: 22 statements → 1 on the test fixture.

**How to apply:** when a plan defers an optimization "unless calibration shows
it matters", check whether the deferral is really about the model's shape. If
it isn't, build it properly now — the calibration consumer is usually the one
that most needs it done right.

## A weight is conditional, a per-metric sort is marginal

Sorting a labelled sample by one feature at a time and reading off how much
signal lands in the top half measures **marginal** discrimination. A weight in
a composite score is a **conditional** quantity — what that feature adds once
the others are known. The two come apart, and the gap runs in the direction
that gets a useful feature deleted.

ghmem's `related_items` is the worked example. At n=54 it sorted signal into
the top half at exactly the 41% base rate, which reads as a coin flip, and it
carried the heaviest weight in `HEAT_WEIGHTS` (3.0) — an apparently damning
pair. At n=198 its fitted coefficient came out +0.243, second largest in the
vector, and zeroing it *cost* corpus-weighted AUC (0.818 against 0.843). It
remains the weakest lone ranker (AUC 0.595). Both facts are true at once.

**Why:** a feature can be uninformative alone and informative in combination —
it separates cases the other features tie, or it corrects them where they
mislead. A one-feature-at-a-time table cannot see that, because it never
conditions on anything.

**How to apply:** to judge a weight, fit the whole vector and compare rankings,
never a per-metric sort. Before cutting a weight, check what dropping it costs
the full score — if the answer is "nothing", that is the evidence; a weak
marginal table is not. And when a sample is stratified on the composite, its
single-metric-versus-composite comparison is handicapped by construction, so
treat any "the part beats the whole" result from it as an artifact until a
differently drawn sample reproduces it.

Related: [[data-model-design]] for the neighbouring "don't build for what the
data cannot produce" judgment.
