# Memory Index

Last consolidated: 2026-04-03

## Topic Files

- **[project-overview.md](project-overview.md)** — Identity (edify-cli dist), structure, CLI groups, plugin bootstrap, rename history
- **[architecture.md](architecture.md)** — Package module map, subpackage structure, plugin submodule, session CLI composition
- **[workflow-system.md](workflow-system.md)** — Plan lifecycle, task model, worktree isolation, session modes, skill system, status display
- **[development-practices.md](development-practices.md)** — Toolchain, testing conventions, code quality, commit patterns, recipes, gotchas
- **[agent-failure-modes.md](agent-failure-modes.md)** — Cached agents, simulated output, test dismissal, directive bypass, competing paths, review convergence
- **[operational-rules.md](operational-rules.md)** — Execution, code quality, session management, delegation, communication rules
- **[decision-index.md](decision-index.md)** — Maps topics to 22 decision files in agents/decisions/
- **[current-state.md](current-state.md)** — Rename nearly done, plugin bootstrap shipped, backlog picking
- **[strategic-pivot.md](strategic-pivot.md)** — 2026-05 teardown: ecosystem (superpowers + autoMemory) replaces homegrown system; future = Lean-assisted requirements tracking. What survives/dies.
- **[verify-loop-direction.md](verify-loop-direction.md)** — 2026-06-08: first concrete mission step = `edify check` (CrossHair) + `formalize` skill; **executed 2026-06-09**, plumbing proven, thesis unproven; living design at `docs/superpowers/design/`.
- **[living-design-doc-structure.md](living-design-doc-structure.md)** — A "living design" doc = numbered FR/NFR + decisions + limitations + history; settled format, don't brainstorm scope.
- **[crosshair-verified-falsification-probe.md](crosshair-verified-falsification-probe.md)** — Don't trust a CrossHair `verified`; probe with a known-false postcondition to rule out unknown-in-disguise.
- **[worktree-submodule-setup.md](worktree-submodule-setup.md)** — Making an EnterWorktree worktree usable: reset to local HEAD, fetch submodules from local stores, venv-on-PATH + sandbox-off quirks.
- **[feedback-deliverable-over-exploration.md](feedback-deliverable-over-exploration.md)** — Reach a requested artifact (bug report, brief) with minimal exploration; report-don't-fix, drop briefs in `plans/`.
- **[pytest-strict-ini-noop.md](pytest-strict-ini-noop.md)** — `[tool.pytest.ini_options]` `strict = true` is a silent no-op; should be `strict_markers` + `strict_config`. Reference config other repos copy — fix to stop propagation.
- **[feedback-squash-metadata-anywhere.md](feedback-squash-metadata-anywhere.md)** — Memory pointer and `.claude/settings.json` can be squashed into whatever commit is convenient; don't ask, don't split out.

## Quick Reference

- **Distribution:** `edify-cli` (PyPI) / `edify` (import)
- **Entry point:** `src/edify/cli.py` (Click)
- **Pre-commit:** `just precommit`
- **Tests:** `just test` (~160 test files, pytest strict)
- **Python:** >=3.14, hatchling build, uv package manager
- **Linting:** ruff ALL + mypy strict + docformatter
- **Plugin infra:** `plugin/` submodule (fragments, skills, agents, hooks)
- **Plans:** 67+ directories in `plans/`, tracked by planstate module
- **Session:** `agents/session.md` — task queue, handoff state
- **Decisions:** `agents/decisions/` — 22 decision files by domain
- **Local dir:** `~/code/claudeutils` (rename to `~/code/edify` pending)
