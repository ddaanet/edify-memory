---
name: edify-commit-hook-gotchas
description: "Committing in edify: gitlore pre-commit + gitmoji commit-msg + async-push lock interactions that cause half-done commits and transient failures"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

Three commit-time hook behaviors in the edify parent repo, learned the hard
way 2026-07-16. They interact, so a naive `git commit` can half-succeed.

**gitmoji commit-msg rejects unrecognized subjects.** The parent's gitmoji
commit-msg hook demands either a standard gitmoji or a conventional prefix
(`build|chore|ci|docs|feat|fix|hotfix|perf|refactor|release|revert|style|test`,
which it rewrites to emoji). `♻️` and `🔥` pass; **`🗑️` was rejected** ("Missing
or malformed conventional commit prefix"). Pick a recognized emoji or a
conventional prefix. The `plugin` submodule has *no* gitmoji hook — any subject
commits there.

**gitmoji runs AFTER gitlore's pre-commit, so a bad subject leaves a half-done
state.** gitlore's pre-commit hook commits (and pushes) the `memory` submodule
*during* the parent commit; the gitmoji commit-msg check runs later. If the
subject is rejected, memory is already committed but the parent aborted — the
memory gitlink is now ahead of what any parent commit records. Recovery: fix
the subject, `git add memory`, and commit again (the gitlink bump rides the
retry). Not a rollback situation.

**The parent commit snapshots a stale memory gitlink → amend.** Even on a
clean commit, because gitlore commits the submodule mid-pre-commit, the parent
records the *pre-hook* gitlink. Follow a memory-touching commit with
`git add memory && git commit --amend --no-edit` to capture the real gitlink
(verify: `git ls-tree HEAD memory` == `git -C memory rev-parse HEAD`). See
[[feedback-squash-metadata-anywhere]] for the gate-message step.

**Post-commit `index.lock` is transient, not stale.** After a gitlore-gated
commit, gitlore pushes memory asynchronously and briefly holds
`.git/index.lock`. An immediately-following git command can fail with "Unable
to create index.lock". It self-clears in ~1–2s — wait and retry; do **not**
reflexively `rm` the lock (a real concurrent git process would also show this).
Confirm no real `git` binary is running (`ps -eo pid,comm | awk '$2=="git"'`)
before ever removing it.

Also: `just precommit` from the agent shell needs `.venv/bin` on PATH
(`PATH=/Users/david/code/edify/.venv/bin:$PATH just precommit`) and
`dangerouslyDisableSandbox` for `uv`; without the venv, ruff/mypy/pytest are
"command not found".
