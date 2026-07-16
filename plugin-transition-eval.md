---
name: plugin-transition-eval
description: "2026-07-15 evaluation of merging the plugin submodule, retiring hooks/fragments/token-efficient-bash, and the blocked release-mechanism decision"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

Multi-agent evaluation run 2026-07-15. Full findings with file:line refs live at
`docs/superpowers/design/plugin-transition-evaluation.md` — read that before
acting; this is the recall hook and the one decision still open.

**Merge `plugin` submodule → parent: recommended, low-risk.** Both rationales are
dead — nothing else on the machine consumes `ddaanet/edify-plugin`, and edify has
**no entry** in `claude-plugins/.claude-plugin/marketplace.json` (so `docs/marketplace.md`
advertising `/plugin install edify@ddaanet` is stale). Even if published, `git-subdir`
(already used for `ddaa`) handles a subdirectory. Coupling is ~5 justfile paths plus
`plugin/bin/check-version-consistency.py`.

**Design now FULLY SPECIFIED 2026-07-16 → `agents/decisions/plugin-packaging.md`.**
The chosen shape is **de-submodule to a plain subdir** (repo root = `edify-cli`
package; `plugin/` stays the plugin root), **not** merge-to-root — merge-to-root was
rejected because a subdir-sourced plugin install copies only the plugin dir, so the
inert `src/` tree must NOT become plugin content. Consequences: `check-version-consistency.py`
needs **no** rewrite (the eval's earlier claim was wrong — it assumed merge-to-root;
`plugin/` stays at the same depth). Plugin gets the CLI at runtime via a `SessionStart`
hook that builds a **stdlib** venv (no `uv`) at `${CLAUDE_PLUGIN_DATA}/venv-<version>/`
and installs `edify-cli==<version>` from PyPI (versions locked equal by the consistency
check); skills invoke it through content-substituted `${CLAUDE_PLUGIN_DATA}/current/bin/edify`.
Forces publish-package-before-plugin ordering. **De-submodule + fragment inlining
EXECUTED 2026-07-16** (commits `c3c4477f` flatten, `11fb877e` inline); the runtime
bootstrap (SessionStart venv hook, PyPI publish, marketplace `git-subdir` entry)
remains unbuilt. Read the decision record before acting.

**`just release` is currently BROKEN and the merge fixes it.** `git add
plugin/.claude-plugin/plugin.json` fails with "Pathspec is in submodule" — a parent
can't stage inside a submodule. Verified by dry-run. Merge removes the boundary. The
pytest-md port landed anyway (`ac5bd788`), test-mock fix `a37b35e4`, `.envrc`
dotenv `a105bc92` — but the recipe can't complete a real release until the submodule
issue is resolved. Also fixed this session: `origin/HEAD` pointed at nonexistent
`origin/tmp` (`git remote set-head origin --auto`).

**Release-mechanism decision RESOLVED 2026-07-16: PyPI-only for now, `claude-plugin-dev`
eventually.** Keep the bespoke recipe / PyPI publish as the release mechanism in the
near term; migrate to `claude-plugin-dev` later (which will require reconciling its
stated edify exclusion — `CLAUDE.md:91-94`, `DESIGN.md:320-325`: no PyPI, no submodule,
no dry-run/rollback, single-manifest — against edify's needs, but that reconciliation is
deferred, not blocking). No longer waiting on a call.

**Hooks: RETIRED 2026-07-16.** Deleted the whole `plugin/hooks/` dir (both hooks +
`hooks.json`). `posttooluse-autoformat.sh` was redundant with `just format`;
`pretooluse-block-tmp.sh` denied writes to `/tmp/` and `/private/tmp/`, which now
*actively conflicts* with the harness scratchpad (user: "made obsolete by harness
scratchpad"). Confirmed the deny list is only NotebookEdit + a datadog WebFetch — so
the hook was the sole tmp enforcement, and `tmp-directory.md`'s settings.json-deny
claim was false. Also rewrote `tmp-directory.md` to point at the harness scratchpad +
project `tmp/` (dropped the false claim and the `/tmp/claude/` prohibition), and
rewrote `plugin/README.md`, which was ~90% fiction (claimed 18 skills/14 agents/23
fragments/4 hooks; reality is 5 skills, 0 agents, 8 fragments, 0 hooks). Also deleted the
dangling symlink `plugin/.claude/agents -> ../agents/` (target `plugin/agents/` doesn't
exist — it surfaced nothing; the live `edify:*` agents come from the parent, not this
link). The valid `plugin/.claude/skills -> ../skills/` symlink is kept.

**`token-efficient-bash`: DELETED 2026-07-16.** Superseded three
ways — the harness/models now write compound Bash spontaneously to cut turns (its
`set -x` trace pattern is moot), the `shell-scripting:shell-gotchas` skill covers its
strict-mode caveats better (source-verified superset: `grep || true`, pipefail+`head`
SIGPIPE), and the `cwd-safety` plugin + shell-gotchas `environments.md` cover its
directory-change reference. Also ships a verified bug (`if grep -q ... || true` makes
the condition always true). Done: removed `plugin/skills/token-efficient-bash/`,
dereferenced it from `CLAUDE.md` and `plugin/README.md`, and reworded the `|| true`
exception at `plugin/fragments/error-handling.md:11` to name the *condition*
(non-zero exit encodes a result: grep no-match, diff differences) instead of the
skill. The prior eval's other reword target — memory `operational-rules.md:16` — was
moot (that memory was deleted in the same 2026-07-16 triage).

**Fragments: inlined into CLAUDE.md 2026-07-16 (done); do NOT move to memory.** Refined 2026-07-16: the
earlier "fragments belong in native memory" instinct was the mistake the previous
migration already made. Fragments are *always-on behavioral rules*; native memory is
*recall-triggered* and may not fire. A rule that must hold every turn (no-praise,
errors-never-silent, delete-don't-archive) cannot live behind a recall gate. The stale
memory `operational-rules.md` was exactly that failed move (a keyless mirror of the
fragments) — deleted in this triage. Correct target post-merge: inline the fragments
into always-loaded CLAUDE.md; memory keeps project *facts/history*, not *rules*.
See [[strategic-pivot]], [[feedback-squash-metadata-anywhere]].
