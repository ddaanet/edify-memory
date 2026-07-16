---
name: edify-docformatter-d205
description: docformatter wraps docstrings at 80 cols — keep the one-line summary ≤70 chars or ruff D205 fights it
metadata: 
  node_type: memory
  type: project
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

edify runs `docformatter` (config in `pyproject.toml`, checked by `just
format`/`just check` via `docformatter -c src tests`) alongside ruff. It
wraps docstrings at 80 columns. A one-line docstring summary longer than ~70
chars gets wrapped onto a second line, which then trips ruff's D205
("1 blank line required between summary line and description"). Keep summary
lines ≤70 chars: 4 indent + 3 `"""` + 70 + 3 `"""` = 80. The only survivor of
the deleted `development-practices` memory — the `PurePath.full_match` and
`git status --porcelain` gotchas died with the torn-down `git`/path modules.
