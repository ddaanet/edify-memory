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
`plugin/bin/check-version-consistency.py` (climbs `parent.parent.parent`, hard-codes
name `plugin` — needs a path rewrite).

**`just release` is currently BROKEN and the merge fixes it.** `git add
plugin/.claude-plugin/plugin.json` fails with "Pathspec is in submodule" — a parent
can't stage inside a submodule. Verified by dry-run. Merge removes the boundary. The
pytest-md port landed anyway (`ac5bd788`), test-mock fix `a37b35e4`, `.envrc`
dotenv `a105bc92` — but the recipe can't complete a real release until the submodule
issue is resolved. Also fixed this session: `origin/HEAD` pointed at nonexistent
`origin/tmp` (`git remote set-head origin --auto`).

**Release-mechanism decision is BLOCKED on the user.** Directive was "use
`claude-plugin-dev`", but that toolkit names edify an explicit non-goal
(`CLAUDE.md:91-94`, `DESIGN.md:320-325`): no PyPI, no submodule, no dry-run, no
rollback, single-manifest model. Options: extend it against its own decision, keep the
bespoke recipe, or reconsider the exclusion now that a merged edify is a different
shape. Not actionable without the call.

**Hooks:** delete `posttooluse-autoformat.sh` (redundant with `just format`).
`pretooluse-block-tmp.sh` is the SOLE mechanical tmp enforcement — `tmp-directory.md:7`'s
claim that settings.json denies `Write(/tmp/*)` is **false** (deny list is only
NotebookEdit + a datadog WebFetch). Couple that hook's fate to the tmp-policy decision;
the prose also fights the harness scratchpad. Delete the dangling symlink
`plugin/.claude/agents -> ../agents/` regardless — it registers the parent's docs as
`edify:*` agents (visible live under `just claude`).

**`token-efficient-bash`: delete (user-confirmed 2026-07-16).** Superseded three
ways — the harness/models now write compound Bash spontaneously to cut turns (its
`set -x` trace pattern is moot), the `shell-scripting:shell-gotchas` skill covers its
strict-mode caveats better (source-verified superset: `grep || true`, pipefail+`head`
SIGPIPE), and the `cwd-safety` plugin + shell-gotchas `environments.md` cover its
directory-change reference. Also ships a verified bug (`if grep -q ... || true` makes
the condition always true). Before deleting, reword the `|| true` exception at
`error-handling.md:11` (fragment) to name the *condition* (non-zero exit encodes a
result: grep no-match, diff differences) instead of the skill. The second reword
target from the prior eval — memory `operational-rules.md:16` — is moot: that memory
was deleted in the 2026-07-16 triage.

**Fragments: inline into CLAUDE.md, do NOT move to memory.** Refined 2026-07-16: the
earlier "fragments belong in native memory" instinct was the mistake the previous
migration already made. Fragments are *always-on behavioral rules*; native memory is
*recall-triggered* and may not fire. A rule that must hold every turn (no-praise,
errors-never-silent, delete-don't-archive) cannot live behind a recall gate. The stale
memory `operational-rules.md` was exactly that failed move (a keyless mirror of the
fragments) — deleted in this triage. Correct target post-merge: inline the fragments
into always-loaded CLAUDE.md; memory keeps project *facts/history*, not *rules*.
See [[strategic-pivot]], [[feedback-squash-metadata-anywhere]].
