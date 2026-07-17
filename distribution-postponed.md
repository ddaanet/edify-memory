---
name: distribution-postponed
description: "2026-07-17: edify-cli PyPI publish and the marketplace entry are postponed indefinitely; the bootstrap is built and verified, distribution just isn't happening"
metadata: 
  node_type: memory
  type: project
  originSessionId: 2e6cf230-f479-49b5-8ba8-8ff59505a17b
---

**Publishing edify is postponed indefinitely (user decision, 2026-07-17).** Do
not propose `just release`, a PyPI upload, or adding the marketplace entry
unless the user reopens it. The work is *done and parked*, not in progress.

**What is finished:** the plugin's `SessionStart` uv-venv bootstrap
(`plugin/bin/bootstrap-venv.sh`) is implemented, unit-tested with a stubbed uv,
and verified end-to-end on 2026-07-17 against a locally built wheel — uv
provisioned CPython 3.14.3, installed `edify-cli` 0.0.2 + 38 deps, and
`current/bin/edify --version` answered. `just bootstrap-check` reruns that loop
offline; see `agents/decisions/plugin-packaging.md`.

**What stays undone, and its ordering** (relevant only if publishing reopens):
`edify-cli` is not on PyPI → the marketplace manifest at
`/Users/david/code/claude-plugins` has **no `edify` entry** (the archived
`ddaanet/edify-plugin` row is gone), so edify is uninstallable today. The entry
would be a `git-subdir` source, `url: ddaanet/edify`, `path: plugin`. Package
must reach the index before the plugin version that pins it, since the hook
installs `edify-cli==<plugin version>`.

**Consequence for the open `${CLAUDE_PLUGIN_DATA}`-substitutes-in-skill-content
gate:** it can only be settled by installing the plugin from a marketplace, so
it stays open for as long as this postponement holds. Don't plan around
resolving it.

See [[plugin-transition-eval]], [[feedback-local-dogfood-no-registry-push]].
