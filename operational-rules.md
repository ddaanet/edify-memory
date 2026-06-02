# Operational Rules

Key behavioral rules that govern agent execution in this project. These are the rules most frequently violated or most impactful when missed.

## Execution

- **Stop on unexpected results** — describe expected vs observed, then STOP
- **Wait for explicit instruction** — don't proceed with plans unless user says "continue"
- **Stop at boundaries** — complete assigned task, no scope creep
- **Skill-gated production edits** — /inline or /orchestrate required for corrector gates

## Code Quality

- **Edit source, not generated output** — agent-core generates agents/skills; edit templates not output
- **Delete obsolete code** — no archiving, no commenting out; git history is the archive
- **Errors never pass silently** — no `|| true`, no `2>/dev/null` (except token-efficient bash expected exits)
- **No sed escape** — when Edit tool fails twice, stop and report; don't bypass to sed

## Session Management

- **Plan-backed tasks mandatory** — every pending task references `plans/<slug>/`
- **Worktree-tasks-only on main** — only trivial fixes belong in-tree
- **Handoff captures conclusions** — if it's not in session.md, it doesn't survive
- **Max 5 concurrent worktrees** — human burnout prevention

## Delegation

- **Recall by file path, not inline** — don't paste resolved content in delegation prompts
- **Flat trigger list for sub-agents** — they can't judge relevance (no pipeline-model artifacts)
- **Quiet execution pattern** — agents report to files, return only filepath or error
- **Resume before relaunch** — stopped agents retain expensive context

## Communication

- **No hedging or preamble** — state information directly
- **Reference, never recap** — assume reader has context
- **No estimates unless requested** — report measured data only
- **No confabulation** — invented heuristics flagged as "ungrounded"
- **Report observable state** — don't filter known-dirty files as "always dirty"
