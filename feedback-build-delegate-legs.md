---
name: feedback-build-delegate-legs
description: "delegating `/build` to an opus orchestrator: a fresh delegate per leg, never `SendMessage`-resumed past a gate; it passes no `name` to its dispatches; never `TaskStop` it while a dispatch is live (the dispatch runs on orphaned)"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 4fb27c8c-dde4-4794-ada5-01579b2f9835
  modified: 2026-10-07T13:05:53.641Z
---

When `/build` runs in a delegated `general-purpose` opus orchestrator:

- **Cycle the delegate.** After a gate or a leg ends, start a fresh delegate
  from the committed leg report and the item-report history; do not
  `SendMessage` the old one to carry on. A resumed orchestrator re-enters with
  its full prior context (legs ran 175k–250k tokens), and the skill resumes
  from git anyway. Answering a gate *inside* the same leg by `SendMessage` is
  still right when the delegate has room.
- **No dispatch names.** Tell the delegate never to pass `name` to the Agent
  tool, overriding any skill text that says to name dispatches: keep the
  composed dispatch name only as the report filename, and resume by agentId.
  A named dispatch joins the session roster, and its hand-back lands in the
  main session, not the delegate's.
- **Never stop a delegate with a live dispatch.** `TaskStop` on the
  orchestrator does not stop its running dispatch, which keeps editing the
  tree as an orphan. Let it reach a clean point first. Check `ListAgents` for
  live teammates before stopping anything.

**Why:** my human partner corrected each one during the review-fixes-2 run
(2026-10-05): "build delegate must be cycled", "it should also not name its
sub-agents", and "let it finish its current work, it has a running teammate"
after a stop that orphaned an in-flight test review.

**How to apply:** put all three in every build-delegate prompt, and name the
leg report (`plans/<job>/reports/build-legN.md`) as the hand-off artefact. See
[[cc-nested-subagent-spawning]].
