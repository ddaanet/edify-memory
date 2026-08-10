---
name: workflow-pipeline-revival
description: "2026-08-04: the torn-down design→runbook→orchestrate pipeline was revived from the edify-plugin GitHub repo and rewired off the dead recall/session subsystems"
metadata: 
  node_type: memory
  type: project
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-04T13:25:01.444Z
---

Reversing part of [[strategic-pivot]]. On 2026-08-04 the workflow pipeline was
revived wholesale, at user request, after the 2026-05 teardown.

**Where the purged content lives.** The plugin was a submodule
(`git@github.com:ddaanet/edify-plugin.git`, formerly `agent-core`) before it was
de-submoduled at `c3c4477f`. That de-submodule left no history behind:
`.git/modules` holds only `gitlore-memory`, `plugin/.git` is an empty `git init`,
and the pre-teardown commits are not reachable from the parent repo at all. **The
repo still exists on GitHub** — clone it to recover anything. Two purge commits
matter: `99920f4` in edify-plugin (145 files, 23,336 deletions) and `0ab890b9`
in edify (agents/decisions, .claude/rules, plans). Restore from `0f6ccc2`, the
commit before the plugin purge.

**What came back:** 6 skills (`design`, `runbook`, `review-plan`, `orchestrate`,
`inline`, `review`), all 13 agents into `plugin/agents/`, 7 docs into
`plugin/docs/`, `prepare-runbook.py` + `validate-runbook.py` +
`assemble-runbook.py` + `create-plan-agent.sh`, 12 workflow-mechanics fragments,
16 decision docs, 3 `.claude/rules`.

**What deliberately did not:** the recall subsystem, `session.md`, and the skills
the ecosystem now covers.

**The rewiring is the load-bearing part.** Restoring verbatim leaves ~150 dead
references. The mapping used:

- `edify _recall resolve` → Read `memory/MEMORY.md`, then Read the files it
  indexes. The recall *artifact* survives as a concept because subagents do not
  inherit the parent session's injected memories — it is how planning-stage
  context reaches execution-stage agents — but it now lists file paths instead
  of trigger keys. `prepare-runbook.py`'s resolution was rewritten from a
  subprocess call to file Reads.
- `/commit` → `/commit-commands:commit`; `/handoff` and `/shelve` →
  `/handoff:handoff`; `/worktree` → `superpowers:using-git-worktrees`;
  `/codify` → `/claude-md-management:revise-claude-md`.
- `agents/session.md` → `.claude/handoff-task.md`; `memory-index.md` →
  `memory/MEMORY.md`; `/plan-adhoc` and `/plan-tdd` → `/runbook`.

**Caveat that survives the session:** the revived pipeline has **no test
coverage** — the passing suite is the CLI's, nothing exercises
`prepare-runbook.py` — and it was verified by reference-sweep, not by running a
plan end-to-end through `design → runbook → orchestrate`. That end-to-end run is
the real check and has not happened.
