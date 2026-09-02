---
name: python-standards
description: "uv, click, pytest, ruff-ALL, mypy strict; this repo is the reference config other projects copy; justfile shape (bare tool names, `sync` as a prolog function, no `just sync`) and the 400-line check_line_limits.sh gate"
metadata:
  node_type: memory
  type: reference
---

This repo is the reference for Python tooling standards: its
`pyproject.toml` and `justfile` carry usage-hardened settings that other
projects copy.

Key points: uv-managed project; click for CLIs (preferred over argparse for all
but the simplest scripts); pytest (+pytest-mock) over stdlib unittest;
`ruff select = ["ALL"]` with this repo's curated ignore list; `mypy strict =
true` + `extra_checks`; docformatter with 80-col wrap; pydocstyle via ruff's D
rules (pep257 convention); justfile `green` recipe pattern (format → ruff →
docformatter -c → mypy → pytest with input-hash sentinel). "No pip
dependencies" is NOT a standing constraint — dependency-using code held to
these standards is preferred over stdlib-only austerity.

**Justfile shape** (this repo and pytest-md agree; verified 2026-07-17 by
reading both):

- Recipes call tools by **bare name** (`ruff`, `mypy`, `pytest`,
  `docformatter`) — they come from the direnv-activated venv on PATH. Never
  `./.venv/bin/foo` and never `uv run foo`. The `.envrc` does
  `PATH_add "$PWD/.venv/bin"`.
- `sync` is a **bash function in the shared `bash_prolog`**, called bare at the
  top of recipes that need fresh deps — never a `just` recipe. `just sync` does
  not exist in these projects and must not be created. Body:
  `sync () { if [ -w /tmp ]; then uv sync -q; fi; }` — the `[ -w /tmp ]` probe
  is the sandbox guard (Claude Code's uv cache is read-only), and it works:
  /tmp is genuinely unwritable in-sandbox. Do not reinvent this with
  `$SANDBOX_RUNTIME`.
- `setup` is the name for the public one-time dep/install job, if one is needed
  at all.
- Prolog also carries `safe`/`end-safe` (run every check, report all failures,
  still exit non-zero) and `show`/`visible`/`fail`.
- **`scripts/check_line_limits.sh`** — a hard 400-line `wc -l` gate over `src`
  and `tests`, wired into the gate recipe. Present here, in home, and in
  pytest-md. This is the only sampled repo where every module is under 400
  lines, and that is causal: ungated repos drift to 600–1200. Ruff's complexity
  rules (C901/PLR0912/PLR0915, active under `select=ALL`) do NOT substitute — a
  file can be 1200 lines of small functions and pass them all. Only a file-line
  gate catches breadth.
- Two tiers run here: `green`/`lint` relax complexity rules for red/green TDD
  iteration; `precommit`/`release` enforce them plus the line cap.

Related: [[line-caps]] for what to do when a file hits the 400-line gate.
