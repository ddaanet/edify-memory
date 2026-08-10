---
name: feedback-grounding-reports-are-reusable
description: "Grounding and external-research reports are reusable reference material; experiment output is the deletable category — don't offer surveys for deletion or re-raise them"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 558659ec-0449-4019-b178-2dbca12a6cee
  modified: 2026-08-10T16:18:17.920Z
---

Tier: global candidate (research-artifact lifecycle, not edify-specific).

When purging obsolete material, research artifacts split into two categories
and only one is a deletion candidate.

**Experiment output is deletable** once its question is answered or abandoned:
corpora, variant files, ground-truth fixtures, harness scripts, probe
transcripts. It has no value beyond the experiment.

**Grounding reports are not.** External-research syntheses and
internal-codebase surveys — `plans/reports/*-grounding.md`,
`*-external-research.md`, `*-internal-codebase.md` — are literature and survey
work whose conclusions outlive the feature that prompted them, and they are
expensive to reproduce. Keep them even when the subsystem they were written for
is gone. Do not offer them for deletion, and do not carry them in a status
summary as an open question.

A grounding report whose *claims* have gone stale is a correction job, not a
deletion job — see [[feedback-stale-claims-survive-reference-sweeps]].
