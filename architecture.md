---
name: architecture
description: Package module map, subpackage structure, plugin submodule, session CLI composition surface
type: project
---

# Architecture

## Package Structure

```
src/edify/
├── cli.py              # Click entry point, all subcommand registration
├── compose.py          # Config composition
├── discovery.py        # Session listing/discovery
├── exceptions.py       # EdifyError hierarchy
├── extraction.py       # Recursive extraction
├── filtering.py        # Content filtering/categorization
├── git.py / git_cli.py # Git operations + CLI
├── markdown*.py        # Markdown processing (core, block, inline, list, parsing)
├── models.py           # Pydantic models (FeedbackItem etc.)
├── parsing.py          # Content parsing
├── paths.py            # Path encoding/resolution
├── token_cache.py      # Token count caching
├── tokens.py / tokens_cli.py  # Token counting + CLI
├── user_config.py      # User configuration
├── account/            # Account management (keychain, providers, state, usage)
├── hooks/              # Hook scripts (stop_status_display.py, sessionstart-health.sh bootstrap)
├── model/              # Model config, overrides, structure
├── planstate/          # Plan lifecycle tracking (aggregation, inference, vet)
├── recall/             # Decision recall system (index parsing, relevance, topics)
├── recall_cli/         # Recall CLI (artifact management)
├── session/            # Session management
│   ├── cli.py / commit.py / commit_gate.py / commit_pipeline.py / parse.py
│   ├── handoff/        # Handoff pipeline (cli, parse, pipeline)
│   └── status/         # Status rendering (cli, render)
├── statusline/         # Status line display (API usage, context, models, plans)
├── validation/         # 18 validator modules (session, memory, tasks, plans)
├── when/               # "When" resolver (compress, fuzzy, index parsing)
└── worktree/           # Worktree management (merge, resolve, remerge, display, git_ops)
```

## Design Patterns

- **CLI convention split:** User-facing (stderr errors, text/JSON) vs LLM-native (all stdout markdown, exit codes)
- **Underscore prefix:** Internal/LLM commands hidden from `--help`
- **Path handling:** `Path.cwd()` for defaults, pathlib throughout
- **Mock patching:** Patch at usage site, not definition site
- **Validation:** Dedicated validator per concern area (18 modules)

## Session CLI Composition Surface

LLM-native CLIs designed for skill composition (structured markdown stdin → stdout):

| CLI | Input | Handles | Skill Retains |
|-----|-------|---------|---------------|
| `_commit` | Stdin: `## Files`, `## Message`, `## Options`, `## Submodule` | Staging, validation gates (precommit/lint/vet), submodule commits | Discovery, message drafting, gitmoji, settings triage |
| `_handoff` | Stdin: `**Status:**`, `## Completed This Session` | Status line overwrite, completed section write (mode detection) | Task carry-forward, blockers, learnings, plan-archive (CLI handles 2 of ~6 sections) |
| `_status` | Reads session.md + plans/ | Full STATUS rendering, parallel detection, plan-derived commands | Behavioral spec only (when to trigger) |
| `_git changes` | None | Unified parent + submodule status/diff | N/A (pure query) |

## Plugin Submodule

Shared infrastructure across projects (formerly `agent-core`, renamed to `plugin` on 2026-03-31):
- `fragments/` — Behavioral rules injected into CLAUDE.md via `@` references
- `skills/` — Workflow skills (design, runbook, orchestrate, handoff, commit, etc.)
- `agents/` — Sub-agent definitions for delegation
- `hooks/` — PreToolUse, PostToolUse, UserPromptSubmit hooks
- `bin/` — Utility scripts (prepare-runbook.py, task-context.sh)
- `templates/` — Generation templates
- Remote: `git@github.com:ddaanet/edify-plugin.git`
