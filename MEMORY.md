# Memory Index

Last consolidated: 2026-07-16 (previous-migration triage — deleted 7 stale
torn-down-system files, split remaining into project-local vs global-candidate
tiers ahead of gitlore nested-memory support).

## Project-local

- **[strategic-pivot.md](strategic-pivot.md)** — 2026-05 teardown: ecosystem (superpowers + autoMemory) replaces the homegrown system; future = Lean-assisted requirements tracking. What survives/dies.
- **[verify-loop-direction.md](verify-loop-direction.md)** — 2026-06-08: first concrete mission step = `edify check` (CrossHair) + `formalize` skill; **executed 2026-06-09**, plumbing proven, thesis unproven; living design at `docs/superpowers/design/`.
- **[plugin-transition-eval.md](plugin-transition-eval.md)** — 2026-07-15/16 eval: merge plugin submodule (fixes broken `just release`), retire hooks, delete token-efficient-bash, inline fragments into CLAUDE.md not memory; release-mechanism decision blocked on user.
- **[living-design-doc-structure.md](living-design-doc-structure.md)** — A "living design" doc = numbered FR/NFR + decisions + limitations + history; settled format, don't brainstorm scope.
- **[crosshair-verified-falsification-probe.md](crosshair-verified-falsification-probe.md)** — Don't trust a CrossHair `verified`; probe with a known-false postcondition to rule out unknown-in-disguise.
- **[edify-docformatter-d205.md](edify-docformatter-d205.md)** — docformatter wraps docstrings at 80 cols; keep summary lines ≤70 or ruff D205 fights it.
- **[edify-commit-hook-gotchas.md](edify-commit-hook-gotchas.md)** — Committing here: gitmoji rejects unrecognized subjects, gitlore commits memory mid-pre-commit (stale gitlink → amend; bad subject → half-done), post-commit index.lock is transient. `just precommit` needs `.venv/bin` on PATH.
- **[worktree-submodule-setup.md](worktree-submodule-setup.md)** — Making an EnterWorktree worktree usable: reset to local HEAD, fetch submodules from local stores, venv-on-PATH + sandbox-off quirks. (Dies with the plugin-submodule merge.)

## Global candidates

Cross-project learnings, marked `Tier: global candidate` in-file. Relocate to
the global tier when gitlore nested memory lands; today they live here because
per-project is the only tier available.

- **[agent-failure-modes.md](agent-failure-modes.md)** — Model-general agent failures: cached agent defs, unverified "pre-existing" test failures, delta-scoped reviews, incomplete raise→catch fixes.
- **[feedback-deliverable-over-exploration.md](feedback-deliverable-over-exploration.md)** — Reach a requested artifact (bug report, brief) with minimal exploration; report-don't-fix, drop briefs in `plans/`.
- **[feedback-squash-metadata-anywhere.md](feedback-squash-metadata-anywhere.md)** — Metadata (memory pointer, `.claude/settings.json`) can be squashed into whatever commit is convenient; don't ask, don't split out.
- **[pytest-strict-ini-noop.md](pytest-strict-ini-noop.md)** — `[tool.pytest.ini_options]` `strict = true` is a silent no-op; should be `strict_markers` + `strict_config`. Reference config other repos copy — fix to stop propagation.

## Quick Reference

- **Distribution:** `edify-cli` (PyPI) / `edify` (import)
- **Entry point:** `src/edify/cli.py` (Click)
- **Python:** >=3.14, hatchling build, uv package manager
- **Recipes:** `just precommit` (gate) · `just test` · `just dev`
- **Live source:** slim — `src/edify/` has no subpackages; CLI = session scraping, `tokens`, `markdown`, `check` (CrossHair)
- **Memory:** `memory/` is a gitlore-gated submodule — persist by writing files and committing the PARENT; never commit inside the submodule.
