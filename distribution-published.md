---
name: distribution-published
description: "edify has no user base, so compatibility is never a reason to keep anything; and the PyPI first-upload token rule for a new project"
metadata: 
  node_type: memory
  type: project
  originSessionId: af34ef60-1ce0-4bf5-afdf-51883d81f3b8
  modified: 2026-08-28T08:40:26.269Z
---

**Edify has no user base.** `edify-cli` is on PyPI and the plugin is in the
`claude-plugins` marketplace, but nothing except this repo installs either.
Compatibility with other Claude Code versions or other installs is therefore
never a reason to keep something: breaking changes, capability strips and
format changes are free, and are decided on what is correct for the harness in
front of you.

**PyPI token gotcha, if a future project is ever created from here:** the first
upload of a *new* PyPI project cannot use a project-scoped token — PyPI only
scopes tokens to projects that already exist. Decode a token's caveats to tell
which kind it is: a project-scoped one carries `[1,["<name>"]]` +
`[2,["<uuid>"]]`, an account-scoped one carries only `[3,"<user-uuid>"]`. The
token in `.env` is account-scoped. The token-free alternative is a PyPI
*pending trusted publisher*, but it requires publishing from GitHub Actions —
OIDC has no local form, and this repo has no `.github/workflows`.

See [[plugin-transition-eval]], [[feedback-local-dogfood-no-registry-push]].
