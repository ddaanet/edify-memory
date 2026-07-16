---
name: feedback-squash-metadata-anywhere
description: "Metadata changes (memory pointer, .claude/settings.json) can be squashed into whatever commit is most convenient"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

**Tier: global candidate** — the squash preference is general; the gitlore-gate and `just release` mechanics named below are edify-specific and would stay behind. Relocate the preference to the global tier when gitlore nested memory lands.

Incidental metadata changes — the `memory` submodule pointer, `.claude/settings.json` — do not need a commit of their own or a matching subject. Squash them into whatever commit is most convenient, including amending an unrelated commit that just landed. Stated this session: "just amend the settings and memory changes to whatever was committed last."

**Why:** These files are session bookkeeping, not project history. Keeping them dirty to preserve a tidy commit boundary costs more attention than the tidiness is worth, and a dirty tree blocks tools that gate on it — `just release` aborts on `git diff --quiet HEAD`, and its dry-run `git reset --hard` makes uncommitted work a hazard.

**How to apply:** Don't ask which commit metadata belongs in, and don't split it out. Fold it into the current or last commit and say so in the report. Still name what the diff actually does when it changes behavior (e.g. dropping `permissions.allow` entries means those tools start prompting again) — the observable-state-reporting rule lives in CLAUDE.md's `communication` fragment. Committing the memory pointer still goes through the gitlore gate: summarize, write `$(git -C memory rev-parse --git-path gitlore-commit-msg)`, then commit the parent — never commit inside the submodule.
