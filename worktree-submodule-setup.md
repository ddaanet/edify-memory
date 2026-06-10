---
name: worktree-submodule-setup
description: How to make an EnterWorktree worktree usable in edify (submodule + venv + sandbox quirks)
metadata: 
  node_type: memory
  type: project
  originSessionId: 5efcbf63-e54e-45da-a5ef-6b88249481be
---

Standing up a usable git worktree in edify hits three non-obvious snags. Encountered 2026-06-08 setting up `.claude/worktrees/verify-loop`.

**1. EnterWorktree branches from `origin/<default>`, not local HEAD.** Its `worktree.baseRef` is a *harness* setting, not the `git config worktree.baseRef` knob (setting that git config did nothing). Local `main` was 11 commits ahead of `origin/main`, so the worktree lacked the plan/spec. Fix: after EnterWorktree, `git reset --hard main` to bring the branch to local HEAD.

**2. The `plugin` and `memory` submodules carry unpushed local commits**, so `git submodule update --init` fails (`not our ref`). The local object stores are `.git/modules/agent-core` (plugin) and `.git/modules/gitlore-memory` (memory) — note the names differ from the submodule paths. Fix per submodule: `git -C plugin fetch -q /Users/david/code/edify/.git/modules/agent-core <sha> && git -C plugin checkout -q <sha>`. Use the **HEAD-recorded** gitlinks (`git ls-tree HEAD plugin memory`), not the branch tips `git submodule status` reports, or you get pointer drift. The `plugin` submodule is required — the root justfile `import 'plugin/portable.just'`.

**3. Env quirks for running anything:** direnv is NOT active in non-interactive shells, so `just test`/`just precommit` can't find `pytest` — prefix `PATH="$PWD/.venv/bin:$PATH" just test ...`. And `uv` commands (sync, run) fail under the sandbox with "Read-only file system" on `~/.cache/uv` — run those with `dangerouslyDisableSandbox: true`. Plain `just test` (pytest only, offline) works inside the sandbox.

**4. `just precommit` needs more than the venv in a fresh worktree** (hit 2026-06-09 finishing verify-loop). Three more manual steps:
- **`tmp/` must exist** — the justfile uses `mktemp tmp/justfile-XXXXXX`; absent dir → `mktemp: No such file or directory`. `mkdir -p tmp`.
- **npm deps must be installed** — `remark-cli` is a local devDependency; a fresh worktree has no `node_modules`, so `shutil.which("remark")` fails and the 16 `test_full_pipeline_remark` cases SKIP. The precommit test gate rejects ANY skip ("Tests skipped — all tests must run"), so precommit fails. Fix: `npm install`, then put `node_modules/.bin` on PATH for the run: `PATH="$PWD/.venv/bin:$PWD/node_modules/.bin:$PATH" just precommit`. Healthy baseline is **293/294 passed, 1 xfail, 0 skipped**.

**FIX SOON (David, 2026-06-09):** fold steps 1–4 into worktree creation / `just setup` so a fresh `.claude/worktrees/*` can run `just precommit` green with no manual prep — currently it takes reset-to-HEAD, submodule checkout from local stores, `mkdir tmp`, `npm install`, and a two-dir PATH prefix.

See [[verify-loop-direction.md]].
