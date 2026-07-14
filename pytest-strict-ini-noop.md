# pytest `strict = true` ini key is a no-op

`pyproject.toml` `[tool.pytest.ini_options]` sets `strict = true`. Pytest
has **no** `strict` ini option, so the key is silently ignored — the
config claims strictness it doesn't enforce (MEMORY.md's Quick Reference
even advertises "pytest strict"). Only `[tool.mypy]`'s `strict = true` is
real; the pytest one is not.

The intended keys are:

- `strict_markers = true` — error on unregistered `@pytest.mark.*`
- `strict_config = true` — error on unknown ini keys (this one would have
  caught the typo in the first place)

**Fix:** replace `strict = true` under `[tool.pytest.ini_options]` with
those two keys. This config is the reference other repos copy verbatim
(micro/tools/ghmem hit and fixed the same bug), so fixing it here stops
the no-op from propagating.
