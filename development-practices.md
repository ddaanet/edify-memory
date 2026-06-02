# Development Practices

## Toolchain

- **Build:** hatchling
- **Package manager:** uv
- **Linting:** ruff (ALL rules enabled, ~30 specific ignores)
- **Type checking:** mypy (strict mode, extra_checks)
- **Formatting:** ruff format + docformatter (wrap at 80 chars)
- **Testing:** pytest (strict mode, --tb=short, -m 'not e2e')
- **Pre-commit:** `just precommit` — runs sync, validators, checks, pytest, line limits

## Testing Conventions

- ~160 test files mirroring source module structure
- TDD cycles: RED (failing test) → GREEN (minimal fix) → REFACTOR
- Mock patching: always patch at usage site, not definition site
- Test sentinel: makes precommit fast when suite is green
- docformatter gotcha: keep docstring summaries ≤70 chars (4 indent + 3 `"""` + 70 + 3 `"""` = 80)

## Code Quality

- `ruff.lint.select = ["ALL"]` — comprehensive linting
- `mypy --strict` with `extra_checks` and `allow_redefinition_new`
- Per-file test ignores: S101 (assert), ARG, SLF001, TRY003
- Exclude agent-core, .claude, plans/prototypes from ruff

## Commit Patterns

- Gitmoji prefixes selected via /commit skill
- Commit messages: short, dense, structured (why over what)
- Cycle commits during TDD: `Cycle N.M: description`
- Phase commits during orchestration: `Phase N: description`

## Key Recipes

- `just precommit` — Full validation gate (fast when green, via test sentinel)
- `just test *ARGS` — Run test suite
- `just dev` — Format + precommit
- `just format` / `just lint` / `just check` — Individual checks
- `just clean` — Remove generated artifacts (.edify-venv, .bin, install log, dist)
- `just sync-to-parent` — Sync agent-core skills/agents to .claude/ (requires sandbox disable)

## Gotchas

- `PurePath.match()` doesn't handle `**` matching zero levels — use `PurePath.full_match()` (Python 3.13+)
- `git status --porcelain` output: don't `.strip()` — preserves XY column positions for `line[3:]` path extraction
- `just precommit` output: never truncate (`| tail -N`) — xfail tracebacks look like real failures without summary
- Partial recipe failure: retry the recipe, don't finish remaining steps manually
- docformatter wraps at 80 chars total — keep docstring summaries ≤70 chars to avoid D205 violations
- PEP 758 (Python 3.14): unparenthesized `except A, B:` is canonical; `ruff format` removes added parentheses
