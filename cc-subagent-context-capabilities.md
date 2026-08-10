---
name: cc-subagent-context-capabilities
description: "Measured 2026-08-10: CC subagents DO get the MEMORY.md index and DO have the Skill tool (plugin-local skills resolve); what they lack is the auto-recall body fetch"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-10T05:59:05.527Z
---

Tier: global candidate (Claude Code platform behaviour, not edify-specific).

Measured 2026-08-10 by three zero-tool-call introspection probes plus one
invocation probe, against `general-purpose` subagents in an edify session.

|                     | main session | subagent |
| ------------------- | ------------ | -------- |
| `MEMORY.md` index   | injected     | **injected** |
| memory file bodies  | auto-fetched | **never** |
| `Skill` tool        | yes          | **yes**  |

- **The index reaches subagents**, under the identical label
  `Contents of <path>/memory/MEMORY.md (user's auto-memory, persists across
  conversations):`, i.e. it rides the CLAUDE.md assembly. Confirmed by verbatim
  quotation of content a fresh agent could not fabricate, with zero tool calls.
- **Auto-recall does not run for subagents.** A probe whose prompt named the
  exact topics of two indexed memories got neither body. Subagents have routing
  without fetch: they can decide what is relevant, but must Read it themselves.
- **The `Skill` tool is available**, a 76-skill listing is provided, and both a
  marketplace skill and a project-local plugin skill (loaded via
  `--plugin-dir`, resolving through `plugin/.claude/skills/<name>`) invoked
  successfully.

**Never instruct any agent to Read `memory/MEMORY.md`.** It is already in
context at both levels. Re-read only if it was edited this session or a
compaction dropped it. A gate anchor belongs on the recall *output* (an artifact
write that fires on hit and null paths alike), never on reading the index.

**Why this is worth storing:** `agents/decisions/project-config.md` asserts the
opposite on both counts ("Sub-agents lack Skill tool", and a "How To Recall
Sub-Agent Memory" decision built on injecting the index by hand). Those were
true of the pre-teardown homegrown system and were carried forward into the
[[workflow-pipeline-revival]] rewiring unexamined. See
[[feedback-stale-claims-survive-reference-sweeps]].
