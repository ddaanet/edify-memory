# Project Overview

**edify** — Python CLI toolkit for managing Claude Code agent workflows.

## Identity

- **Distribution:** `edify-cli` (PyPI name, hatchling build with explicit packages config)
- **Package:** `edify` (import name, lives at `src/edify/`)
- **Entry point:** `edify = "edify.cli:main"` (Click-based)
- **Python:** >=3.14
- **Repo:** github.com/ddaanet/edify (directory still `~/code/claudeutils` — rename pending)
- **License:** Not specified in pyproject.toml

## Key Dependencies

- click (CLI framework)
- anthropic SDK (API integration)
- pydantic (data models)
- sqlalchemy (data layer)
- pyyaml (config parsing)

## Repository Structure

```
src/edify/            # Main package (~15 modules + 10 subpackages)
tests/                # ~160 test files
plugin/               # Git submodule (edify-plugin): shared fragments, skills, agents, hooks
agents/               # Session handoff (session.md, learnings.md, decisions/)
plans/                # 67+ plan directories at various lifecycle stages
docs/                 # Project documentation (marketplace.md)
skills/               # Project-specific skills
scripts/              # Utility scripts
bin/                  # Binary scripts
research/             # Research artifacts
```

## What It Does

Provides CLI tooling for an agent orchestration system built on Claude Code:
- **Session management:** handoff, status, commit pipelines
- **Worktree orchestration:** parallel task isolation via git worktrees
- **Recall system:** decision/learning retrieval for agent context injection
- **Validation:** session structure, memory index, learnings, planstate, tasks
- **Token counting:** file token estimation with model-specific encoding
- **Markdown processing:** cleanup, formatting, parsing
- **Plan state tracking:** lifecycle from requirements through delivered

## CLI Subcommand Groups

User-facing: `analyze`, `rules`, `tokens`, `validate`, `compose`, `markdown`
LLM-native (underscore prefix): `_session`, `_worktree`, `_recall`, `_when`, `_commit`, `_handoff`, `_status`

## Plugin Self-Contained Bootstrap

When loaded as a Claude Code plugin (CLAUDE_PLUGIN_ROOT set), SessionStart hook:
1. Installs `edify-cli` from git into `.edify-venv` (isolated from dev venv)
2. Exposes `edify` command via `.bin/` shim directory
3. Exports `EDIFY_PLUGIN_ROOT` via `$CLAUDE_ENV_FILE`

This makes the plugin work standalone without requiring the user to have the dev repo.

## Rename History

Package renamed `claudeutils` → `edify` on 2026-03-31 (SP-1 + SP-2). Submodule renamed `agent-core` → `plugin` (SP-1). Distribution name changed to `edify-cli` on 2026-04-02 (hatchling packaging fix). GitHub repo URL not yet renamed (FR-12). Local directory `~/code/claudeutils` not yet renamed.
