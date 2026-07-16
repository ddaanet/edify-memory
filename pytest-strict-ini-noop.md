---
name: pytest-strict-ini-noop
description: "pyproject [tool.pytest.ini_options] strict = true is a silent no-op — use strict_markers + strict_config instead"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

**Tier: global candidate** — this is the reference config other repos copy
verbatim (micro/tools/ghmem hit and fixed the same bug); belongs in the
global tier when gitlore nested memory lands so the fix stops propagating.

`pyproject.toml` `[tool.pytest.ini_options]` sets `strict = true`. Pytest has
**no** `strict` ini option, so the key is silently ignored — the config
claims a strictness it doesn't enforce. Only `[tool.mypy]`'s `strict = true`
is real. Still present in edify's `pyproject.toml` as of 2026-07-16.

The intended keys are:

- `strict_markers = true` — error on unregistered `@pytest.mark.*`
- `strict_config = true` — error on unknown ini keys (this one would have
  caught the typo in the first place)

**Fix:** replace `strict = true` under `[tool.pytest.ini_options]` with those
two keys.
