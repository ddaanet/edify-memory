---
name: inline-pipeline-tooling-drift
description: "Two gaps found running /inline end-to-end on edify-recall-skill — stale handoff-write instruction and a triage-feedback.sh assumption that doesn't hold for multi-group review dispatch"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4e2b3231-3c15-47f0-bd7c-62058ac3ff2f
  modified: 2026-08-13T16:36:43.465Z
---

Running `/inline` (plugin/skills/inline/SKILL.md) end-to-end on the
edify-recall-skill plan (2026-08-13) surfaced two tooling gaps in the
revived pipeline, distinct from [[workflow-pipeline-revival]]'s original
untested-end-to-end note:

1. **Phase 4c's direct-write instruction is stale.** The skill says "Write
   the pending task directly to `.claude/handoff-task.md`" — but the
   installed `ddaanet/handoff` plugin (0.11.7+) hard-blocks direct
   Write/Edit to that file via a `PreToolUse` write-guard hook
   (`handoff-task.md is written only by handoff-checkpoint`). The only
   sanctioned path is invoking `handoff:handoff`, which writes it through
   `handoff-checkpoint`. `plugin/skills/inline/SKILL.md` Phase 4c needs
   updating to invoke the handoff skill instead of asserting a direct
   Write.

2. **`triage-feedback.sh` assumes one `reports/review.md`.** When Phase 4a
   routes changed files to multiple reviewers by artifact type (this build
   used `review-skills.md`/`review-agents.md`/`review-code.md` for
   skill/agent/code groups, per the routing table in
   `plugin/fragments/review-requirement.md`), the script reports "Reports:
   0" / "Review artifact: none" and warns "review gate may have been
   bypassed" even though every group was reviewed. Verdict still came back
   `no-classification` (not `divergence`), so the run wasn't blocked, but
   the warning is a false positive worth fixing — the script should glob
   `reports/review*.md`, not a fixed filename.

**Why:** Both are pre-existing pipeline tooling, not this build's scope
(edify-recall's plan explicitly excluded exercising the pipeline). They're
the kind of drift the pipeline's own first real exercise was expected to
surface.

**How to apply:** Before the next `/inline` run that reaches Phase 4c or
uses multi-group review dispatch, fix `plugin/skills/inline/SKILL.md`
Phase 4c (route through `handoff:handoff`) and
`plugin/bin/triage-feedback.sh` (glob review reports). Until fixed, expect
the same stale-instruction friction and false-positive warning.
