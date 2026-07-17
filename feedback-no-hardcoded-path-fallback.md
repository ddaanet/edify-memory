---
name: feedback-no-hardcoded-path-fallback
description: Never hardcode paths, especially as a rarely-exercised fallback — obsolete-format rot risk.
metadata:
  node_type: memory
  type: feedback
  originSessionId: 1fd11dae-b50f-41c7-8237-91889ce3f865
---

**Tier: global candidate** — general robustness principle.

Do not hardcode file paths, and *especially* not as a fallback behind a primary
mechanism (env-var substitution, discovery, config). Prefer the substitutable or
discovered form; if it might not work, solve it properly rather than baking in a
literal path as insurance.

**Why:** A fallback path is exercised rarely, so a path *format* that drifts
(directory-layout change, id-encoding rule change) rots silently and fails
exactly when the primary mechanism is already unavailable — the worst moment.

**How to apply:** If the substitutable form's coverage is uncertain, gate on a
real check and pick a substitutable resolution; ship no literal-path fallback.
Origin: edify plugin skills referencing the CLI via `${CLAUDE_PLUGIN_DATA}` — the
proposed `~/.claude/plugins/data/...` hardcoded fallback was struck. See
[[plugin-transition-eval]].
