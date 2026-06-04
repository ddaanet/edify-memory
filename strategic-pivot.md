---
name: strategic-pivot
description: "Edify's future direction and the 2026-05 teardown — what survives, what dies, why"
metadata: 
  node_type: memory
  type: project
  originSessionId: 1cfcebae-b2e5-43e1-99ee-04cbc829bfa9
---

Decided 2026-05-23. Edify's homegrown workflow system is being torn down because the ecosystem caught up: **superpowers** is now the baseline process skillset, and Anthropic's **autoMemory** replaces the entire recall/active-recall/codify/memory-index subsystem.

**Teardown EXECUTED and MERGED TO MAIN (local) 2026-05-23**: started on branch `teardown`, fast-forwarded into `main` in both repos — parent `main` `1404307b` records submodule gitlink `edecf16`; submodule `main` at `edecf16`. **PUSHED to origin 2026-05-24** — both `main` branches in sync with `origin/main` (`0 0` ahead/behind in each). Teardown is now public; no longer reversible as a unit. Note: `submodule.plugin.url` is absent from parent `.git/config`, so `git submodule status` shows a cosmetic `-` prefix; the gitlink and working tree are correct. Final state matches the survives/dies lists below; `error-handling`/`project-tooling` kept as fragments, `token-economy`/`pushback`/`tool-batching` cut (system-prompt duplicates).

**Skill cleanup progress (2026-06-02):** `brief` and `handoff` skills deleted from edify plugin (superseded by `ddaa:brief` and `handoff:handoff`). Remaining: decouple `deliverable-review` + `prioritize`; create `cwd-safety` plugin on ddaanet (hook source: `99920f4^:hooks/submodule-safety.py`). Gitlore installed (`edify-memory` remote, public). `deliverable-review` confirmed: works on informal requirements, no Lean dependency.

Follow-ups RESOLVED 2026-05-23: venv repaired via `uv sync --reinstall` (stale `claudeutils` shebangs in console scripts — `uv sync` alone audited+skipped them); precommit now green (276 pass / 1 xfail / **0 skipped** — the "16 skipped" were an artifact of the broken venv). Skill decoupling done MINIMALLY per user choice: only `ground` + `proof` severed of dangling pipeline refs (`ground` scout-agent/workflow-tier/`/reflect`/old-scraper→`edify list|extract|collect`; `proof` planstate/Corrector Dispatch/Author-Corrector Coupling/Integration Points removed, generic item-review loop kept). The other 5 "surviving" prose skills (`handoff`, `brief`, `prioritize`, `requirements`, `deliverable-review`) are **wholly built on the deleted pipeline** — no stray refs to sever, the skill *is* the ref — so they were deliberately LEFT AS-IS for the rebuild arc to reckon with (don't mistake them for clean). `handoff` is also superseded by the `handoff:handoff` plugin. Next arc = the rebuild; start with a brainstorming pass.

**Edify is NOT abandoned.** Its future is **Lean-assisted requirements tracking** — formal-proof-backed requirements (in the style of "leanstral", a Lean+LLM proof effort the user referenced; verify its actual shape before borrowing). Current state is intentional teardown-to-foundation ("end of winter"), not decay.

**Survives the teardown:**
- 7 prose skills: `handoff`, `brief`, `proof`, `ground`, `prioritize`, `requirements`, `deliverable-review` — framework-agnostic, battle-tested. `requirements` becomes the *seed* of the new mission (rebuilt around formalization + tracking, not just decoupled). `deliverable-review`/`requirements` are currently coupled to `plans/<name>/` layout + planstate + corrector subagents — must be decoupled.
- `token-efficient-bash` (kept for now).
- 3 standalone CLI tools (no task-system coupling, persisting need): **markdown postprocessor**, **token counter**, **session-log scraper** (`extraction.py`). `edify-cli` survives as a slim package around these.
- Salvage-for-thinking (proto-work for formal requirements, even as pipeline plumbing is cut): `system-property-tracing`, `invariant-tracking`, `decision-drift-audit`, `markdown-ast-parser`, `prototypes` (diagnoses what the old scraper got wrong).

**Dies:** the `design→runbook→orchestrate` pipeline + ~25 skills, the recall subsystem, the session.md task-model (should have used gh issues / Notion instead of growing its own), `worktree` (main value was submodule handling — unneeded, little used), and all their CLI plumbing (recall/when, worktree, planstate, session, validation, statusline, git, hooks, compose/discovery/filtering/paths/user_config). `model`/`account`/`statusline` were incomplete unused reimplementations of shell scripts in `../home`. CLAUDE.md collapses to ~9 behavioral fragments (communication, [[pushback→ground]], no-confabulation, no-estimates, error-handling, code-removal, source-not-generated, tmp-directory, token-economy).

See [[current-state]], [[project-overview]], [[workflow-system]] — those describe the system being dismantled and are now largely historical.
