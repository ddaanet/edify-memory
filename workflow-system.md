---
name: workflow-system
description: Plan lifecycle, task model, worktree isolation, session modes, skill system, status display architecture
type: project
---

# Workflow System

## Plan Lifecycle

```
requirements → briefed → outlined → designed → planned → ready → [orchestrate] → review-pending → reviewed → delivered
                                                                                                  ↘ rework → ...
```

Plans live in `plans/<slug>/` directories. State tracked by planstate module (file-presence inference).

## Task Model

- Tasks live in `agents/session.md` under "In-tree Tasks" or "Worktree Tasks"
- Every task must reference a `plans/<slug>/` directory (plan-backed mandate)
- Task format: `- [ ] **Name** — \`command\` | model | restart?`
- Statuses: `[ ]` pending, `[x]` complete, `[>]` in-progress, `[!]` blocked, `[†]` failed, `[-]` canceled

## Worktree Isolation

- Parallel tasks execute in git worktrees (max 5 concurrent unblocked)
- Classification is static — set at creation, no move semantics between in-tree/worktree
- `edify _worktree` CLI manages lifecycle: new, ls, merge, rm
- Main branch is worktree-tasks-only scope (only trivial fixes in-tree)

## Session Modes

| Shortcut | Mode | Behavior |
|----------|------|----------|
| `s` | STATUS | List tasks, wait for instruction |
| `x` | EXECUTE | Resume in-progress or start first pending |
| `xc` | EXECUTE+COMMIT | Execute → handoff → commit → status |
| `r` | RESUME | Strict resume only (error if none) |
| `wt` | WORKTREE | Set up parallel execution |

## Skill System

Skills provide specialized capabilities invoked via `/skill-name`:
- **Workflow:** /design, /runbook, /orchestrate, /inline
- **Session:** /handoff, /commit, /recall, /codify
- **Quality:** /review, /deliverable-review, /review-plan
- **Meta:** /requirements, /proof, /brief, /shelve

## Recall System

Decision files in `agents/decisions/` store institutional knowledge (22 files by domain).
`edify _recall` CLI resolves entries by topic/trigger matching.
Recall artifacts (curated entry lists) are passed to sub-agents by file path, not inline.

## Multi-Sub-Problem Plans

When a design contains multiple independent sub-problems:
- Keep together through design (shared context benefits)
- After design, split into separate tasks with explicit dependencies
- Parent plan delivers at "designed" status (terminal); children start at "planned"
- Each child gets own WSJF score, model tier, worktree classification
- Applies to review scope too — segment deliverables by sub-problem for convergence

## Status Display Architecture

Agent emits `Status.` trigger → Stop hook renders via `_status` CLI:
1. Agent outputs `Status.` as final line of response
2. Stop hook (`src/edify/hooks/stop_status_display.py`) detects `^Status\.$` via `re.fullmatch`
3. Hook runs `edify _status`, returns `systemMessage` with ANSI reset codes per line
4. `additionalContext` tells agent status was displayed (prevents re-render)
5. Loop guard: `stop_hook_active` check prevents infinite recursion

## Key Conventions

- Skill-gated execution: production edits require skill invocation for corrector gates
- Plan-backed tasks: no inline-described tasks allowed
- Integration-first TDD: runbook cycles follow RED → GREEN → REFACTOR
- Gitmoji commits: structured messages via /commit skill
