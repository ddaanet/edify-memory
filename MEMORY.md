# Memory Index

Last consolidated: 2026-07-16 (previous-migration triage — deleted 7 stale
torn-down-system files, split remaining into project-local vs global-candidate
tiers ahead of gitlore nested-memory support).

## Project-local

- **[strategic-pivot.md](strategic-pivot.md)** — 2026-05 teardown: ecosystem (superpowers + autoMemory) replaces the homegrown system; future = Lean-assisted requirements tracking. What survives/dies. **Partially reversed 2026-08-04.**
- **[workflow-pipeline-revival.md](workflow-pipeline-revival.md)** — 2026-08-04: pipeline revived from the `edify-plugin` GitHub repo (the only place the purged history survives) and rewired off dead recall/session subsystems. Untested end-to-end.
- **[verify-loop-direction.md](verify-loop-direction.md)** — 2026-06-08: first concrete mission step = `edify check` (CrossHair) + `formalize` skill; **executed 2026-06-09**, plumbing proven, thesis unproven; living design at `docs/superpowers/design/`.
- **[distribution-published.md](distribution-published.md)** — `edify-cli` 0.0.3 shipped to PyPI via `just release`; marketplace entry still missing; PyPI first-upload token rules. No user base — cross-version compatibility never justifies keeping anything.
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

- **[cc-subagent-context-capabilities.md](cc-subagent-context-capabilities.md)** — What a CC subagent gets: the index but no body fetch, the `Skill` tool, and `Agent` to spawn its own children; what `Agent` lacks; why a declared `tools:` list isn't a contract. Never instruct an agent to Read the index.
- **[feedback-stale-claims-survive-reference-sweeps.md](feedback-stale-claims-survive-reference-sweeps.md)** — Rewiring a doc's references doesn't validate its claims; re-derive capability assertions after a platform change.
- **[agent-failure-modes.md](agent-failure-modes.md)** — Model-general agent failures: cached agent defs, unverified "pre-existing" test failures, delta-scoped reviews, incomplete raise→catch fixes, stray Write closing-tags.
- **[feedback-deliverable-over-exploration.md](feedback-deliverable-over-exploration.md)** — Reach a requested artifact (bug report, brief) with minimal exploration; report-don't-fix, drop briefs in `plans/`.
- **[feedback-squash-metadata-anywhere.md](feedback-squash-metadata-anywhere.md)** — Metadata (memory pointer, `.claude/settings.json`) can be squashed into whatever commit is convenient; don't ask, don't split out.
- **[pytest-strict-ini-noop.md](pytest-strict-ini-noop.md)** — `[tool.pytest.ini_options]` `strict = true` is a silent no-op; should be `strict_markers` + `strict_config`. Reference config other repos copy — fix to stop propagation.
- **[feedback-local-dogfood-no-registry-push.md](feedback-local-dogfood-no-registry-push.md)** — Local dogfooding must never require a PyPI/npm push; install from a local index (build wheel → `pip --find-links`), publish only at release.
- **[feedback-decision-docs-are-living.md](feedback-decision-docs-are-living.md)** — `agents/decisions/*.md` are living design docs: rewire their refs when components change, don't freeze them as ADRs or drop them.
- **[feedback-grounding-reports-are-reusable.md](feedback-grounding-reports-are-reusable.md)** — Grounding/external-research reports outlive their feature and are expensive to redo; delete experiment output, keep surveys, and don't re-raise them.
- **[feedback-no-hardcoded-path-fallback.md](feedback-no-hardcoded-path-fallback.md)** — Never hardcode paths, especially as a rarely-exercised fallback; the stale format rots silently and fails when the primary mechanism is already down.

## Quick Reference

- **Distribution:** `edify-cli` (PyPI) / `edify` (import)
- **Entry point:** `src/edify/cli.py` (Click)
- **Python:** >=3.14, hatchling build, uv package manager
- **Recipes:** `just precommit` (gate) · `just test` · `just dev`
- **Live source:** slim — `src/edify/` has no subpackages; CLI = session scraping, `tokens`, `markdown`, `check` (CrossHair)
- **Memory:** `memory/` is a gitlore-gated submodule — persist by writing files and committing the PARENT; never commit inside the submodule.
