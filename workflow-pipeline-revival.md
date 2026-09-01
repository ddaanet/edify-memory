---
name: workflow-pipeline-revival
description: "stale pipeline names met in an old plan, report or memory: /plan-tdd, /plan-adhoc, /worktree, /codify, /shelve, memory-index.md, agent-core → live names; pre-teardown text = archived edify-plugin repo, commit before its 145-file purge"
metadata:
  node_type: memory
  type: project
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-09-01T20:43:35.219Z
---

**Pre-teardown pipeline text.** The archived `ddaanet/edify-plugin` GitHub
repo (formerly `agent-core`) is the only place it survives; restore from the
commit immediately before its purge commit, the one deleting 145 files
(~23k lines) in 2026-05. The parent repo's matching purge the same day
removed `agents/decisions`, `.claude/rules` and `plans/`. Anything deleted
since the 2026-08 revival is in this repo's own history.

**Stale name → live name**, for the names no other artifact maps:

- `/plan-adhoc`, `/plan-tdd` → `/runbook`
- `/worktree` → `superpowers:using-git-worktrees`
- `/codify` → `/claude-md-management:revise-claude-md`
- `/shelve` → `/handoff:handoff`
- `memory-index.md` → `memory/MEMORY.md`

The 2026-08 rewiring (`edify _recall`, `/commit`, `/handoff`, `session.md`)
is stated in `CLAUDE.md` §Skills and `docs/changelog.md` 2026-08-04; the
2026-09 retirements (`runbook-outline.md`, `prepare-runbook.py`,
`validate-runbook.py`, `/review-plan`, `runbook-outline-corrector`, Tier
1/2/3) in `docs/changelog.md` 2026-09-01 and `docs/design.md` §7. Pipeline
validation status is `docs/design.md` L-6.
