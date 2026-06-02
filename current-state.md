---
name: current-state
description: Project state snapshot — rename nearly done, plugin bootstrap shipped, backlog picking
type: project
---

# Current State (2026-04-03)

## Active Branch

`main` — clean tree, no in-progress work.

## Recent Completed Work

- **Edify rename all agent-executable FRs** (2026-03-31) — SP-1 (submodule rename), SP-2 (package rename), SP-3 (cleanup), FR-9d (rules paths), FR-10 (delivered plan cleanup). All code-side rename work done.
- **Distribution name `edify-cli`** (2026-04-02) — Changed pyproject.toml name to `edify-cli` with explicit hatchling packages config (src/edify/ doesn't match edify_cli/ auto-discovery).
- **Plugin self-contained bootstrap** (2026-04-02) — SessionStart hook installs edify-cli into .edify-venv, .bin/ shim. Plugin works standalone.
- **Marketplace docs** (2026-04-02) — Moved marketplace.md from agents/decisions/ to docs/.
- **`just clean` recipe** (2026-04-02) — Removes .edify-venv, .bin, install log, dist.

## Remaining Rename Work

All external/human-driven:
- **FR-11** — Marketplace listing
- **FR-12** — GitHub repo rename (ddaanet/claudeutils → ddaanet/edify)
- **FR-13** — PyPI PEP 541 name claim
- **FR-9c** — PyPI package name update
- **Directory rename** — `~/code/claudeutils` → `~/code/edify` (requires matching `~/.claude/projects/` directory rename for automemory continuity)

## In-Tree Task Queue

1. **Centralize recall** — opus, prerequisite complete
2. **Outline template trim** — opus
3. **Review skill-CLI** — opus, deliverable review pending
4. **Skill-CLI completion** — opus, commit discovery + test flag + handoff composition

## Backlog Scale

- 60+ worktree tasks queued (sonnet/opus mix)
- 67+ plan directories across lifecycle stages
- Prioritization reference: `plans/reports/prioritization-2026-03-12.md`

## Technical Debt

- **Learnings at ~142 lines** (soft limit 80) — `/codify` overdue
- **SP-2 corrupted learnings.md examples** — mechanical rename changed quoted historical examples
- **pretooluse-recall-check hook:** regex `[^/]+` matches across newlines/spaces

## Unresolved Discussion

**Pipeline direction under active questioning** (first raised 2026-03-28):
- User questioning design → runbook → orchestrate pipeline, behavioral guardrails, planning-first paradigm
- No decisions made — discussion only
- Sessions should not assume pipeline continuity without checking
