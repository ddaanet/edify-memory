---
name: feedback-deliverable-over-exploration
description: "When asked for a concrete deliverable, reach it with minimal exploration first"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5efcbf63-e54e-45da-a5ef-6b88249481be
---

When the user asks for a concrete artifact (a bug report, a brief, a patch), produce it with the least exploration that suffices — don't audit every related file before writing. Flagged this session: "too much exploring, not enough bug reporting."

**Why:** Non-load-bearing exploration burns the user's time and tokens; they would rather get a good-enough artifact and iterate than watch a full survey first.

**How to apply:** Gather just enough to ground the deliverable (for a bug: root cause + the sites a fix must touch), then write it. Add depth only if the artifact proves thin. Bug reports are *briefs dropped in the project's `plans/`* for another session — report, don't fix in place.
