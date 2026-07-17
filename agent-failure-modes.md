---
name: agent-failure-modes
description: "Durable, model-general agent-execution failure modes — cached agent defs, unverified pre-existing failures, delta-scoped reviews, incomplete raise→catch fixes"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4999475a-c8cc-4142-9a21-cb94ed911b38
---

Recurring, expensive-to-debug agent failures. Trimmed 2026-07-16 to the
lessons that are architecturally invariant rather than artifacts of the
retired design→runbook→orchestrate pipeline. **Tier: global candidate** —
none of these are edify-specific; relocate to the global tier when gitlore
nested memory lands. See [[strategic-pivot]] for the pipeline-era failures
that were dropped.

**Cached agent definitions.** Sub-agent system prompts are built at session
start and cached. Editing `.claude/agents/*.md` (or any agent definition)
mid-session has no effect. Symptom: agent returns 0 tool uses, high reported
tokens but a 2–3s duration, fabricated file/test/commit content. Fix: start
a fresh session (or `claude -p`) after changing an agent definition.

**Unverified "pre-existing" failures.** An agent runs scoped tests (one
file), gets green, then at commit time the full suite shows failures and the
agent labels them "pre-existing" without checking. If `just precommit` shows
failures not in the prior commit, they're regressions — verify with
`git stash && just test && git stash pop` before dismissing.

**Delta-scoped reviews inherit blind spots.** Reviewing only what changed
since the last round means each round carries forward every earlier round's
misses. Full-scope every review; re-reading unchanged code is cheaper than
accumulating undetected findings.

**Incomplete fix verification (raise without catch).** Marking an
exception-handling fix "FIXED" by checking the raise site but not the catch
site. A correct new raise with an uncovered `except` is worse than the
original — unstructured crash instead of wrong-but-structured output. Verify
the full propagation path: raise site → intermediate callers → top-level
handler.

**Stray closing-tag literal in Write content.** A `Write` can land a
trailing `</content>` (or similar closing-tag) line in the file that was
never in the intended content. It is silent — the tool reports success and
the file-state echo does not flag it. Linted files catch it (shellcheck
SC1073, ruff syntax error); unlinted scripts/config/data would ship broken.
After writing a file no linter covers, verify its tail.
