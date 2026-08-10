---
name: distribution-published
description: "2026-08-04: edify-cli 0.0.3 is published on PyPI (postponement reversed); bootstrap resolves from PyPI, marketplace entry still missing"
metadata: 
  node_type: memory
  type: project
  originSessionId: af34ef60-1ce0-4bf5-afdf-51883d81f3b8
  modified: 2026-08-10T16:18:24.634Z
---

**`edify-cli` is on PyPI as of 2026-08-04, version 0.0.3.** This reverses the
2026-07-17 "postponed indefinitely" decision — the user reopened it and the
release shipped. `just release` is the mechanism and it works (the plugin
submodule merge fixed it); it bumped 0.0.2 → 0.0.3, propagated the version to
`plugin/.claude-plugin/plugin.json`, tagged `v0.0.3`, pushed, uploaded both
sdist and wheel, and created the GitHub release.

**Verified end-to-end, not just assumed:** running
`plugin/bin/bootstrap-venv.sh` with `UV_FIND_LINKS` unset and a scratch
`CLAUDE_PLUGIN_DATA` resolved `edify-cli==0.0.3` from PyPI, installed 38
packages, and `current/bin/edify --version` answered. The SessionStart hook no
longer fails; no `.envrc` / `UV_FIND_LINKS` workaround is needed.

**PyPI token gotcha, if a future project is ever created from here:** the first
upload of a *new* PyPI project cannot use a project-scoped token — PyPI only
scopes tokens to projects that already exist. Decode a token's caveats to tell
which kind it is: a project-scoped one carries `[1,["<name>"]]` +
`[2,["<uuid>"]]`, an account-scoped one carries only `[3,"<user-uuid>"]`. The
token now in `.env` is account-scoped; re-scoping it to `edify-cli` is
possible now that the project exists. The token-free alternative is a PyPI
*pending trusted publisher* (account sidebar → Publishing, names the
not-yet-existing project), but it requires publishing from GitHub Actions —
OIDC has no local form, and this repo has no `.github/workflows`.

**Still undone:** the marketplace manifest at
`/Users/david/code/claude-plugins` has **no `edify` entry**, so the plugin is
still not installable from a marketplace. The entry would be a `git-subdir`
source, `url: ddaanet/edify`, `path: plugin`. The
`${CLAUDE_PLUGIN_DATA}`-substitutes-in-skill-content gate still can't be
settled until that lands, since it needs a marketplace install to exercise.

**Edify has no user base.** Published on PyPI is not consumed — nothing but
this repo installs the plugin. Compatibility with other Claude Code versions or
other installs is therefore never a reason to keep something: breaking changes,
capability strips and format changes are free, and are decided on what is
correct for the harness in front of you.

See [[plugin-transition-eval]], [[feedback-local-dogfood-no-registry-push]].
