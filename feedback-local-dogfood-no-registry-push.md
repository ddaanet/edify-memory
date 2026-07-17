---
name: feedback-local-dogfood-no-registry-push
description: Local dogfooding must never require a package-registry (PyPI/npm/…) push; develop against a local index.
metadata:
  node_type: memory
  type: feedback
  originSessionId: 1fd11dae-b50f-41c7-8237-91889ce3f865
---

**Tier: global candidate** — packaging principle, not edify-specific.

Never require a registry publish (PyPI, npm, …) to exercise a package locally.
Install from a *local index* instead — e.g. build the wheel and
`pip install --find-links=<dist-dir> pkg==<version>` — prove it green offline,
then publish to the real registry.

**Why:** Publishing a version just to test it burns a public version number and
ships an unexercised artifact; a broken build then needs a yank or superseding
release. Registry publish-ordering constrains *releases*, not development.

**How to apply:** When a bootstrap/installer says it pulls "from PyPI", read it
as "from an index" — point it at a local one for dev/test, flip to the real
registry only at release. Origin: edify plugin's SessionStart venv bootstrap.
See [[plugin-transition-eval]].
