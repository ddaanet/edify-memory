---
name: cc-subagent-context-capabilities
description: "What a Claude Code subagent gets: the MEMORY.md index but no auto-fetched body, the Skill tool, and the Agent tool to spawn its own subagents — plus the parameters Agent does not have"
metadata:
  node_type: memory
  type: reference
  originSessionId: 6fbb09f6-0327-477b-8a3c-cc5dae32f4bd
  modified: 2026-08-12T20:25:19.684Z
---

Observed on CC 2.1.226 across `general-purpose` and custom plugin agent types.

|                    | main session | subagent |
| ------------------ | ------------ | -------- |
| `MEMORY.md` index  | injected     | injected |
| memory file bodies | auto-fetched | never    |
| `Skill` tool       | yes          | yes      |
| spawn a subagent   | yes          | yes      |

**The index reaches subagents** under the same `Contents of <path>` label the
main session gets, riding the CLAUDE.md assembly. **Auto-recall does not run
below the main session**, so a subagent holds routing without fetch: it can
judge what is relevant but must Read the body itself. Never instruct any agent
to Read `memory/MEMORY.md` — not even after editing it (the edit's own diff
already carries the new content) or after a compaction: a live test (two
compaction boundaries, one planted marker) showed compaction neither drops nor
refreshes the injected index — it stays a frozen snapshot from session start,
so there is nothing a conditional re-read could key off (D7,
`docs/superpowers/specs/2026-08-11-edify-recall-skill-design.md`). A gate
anchor belongs on the recall *output*, never on reading the index.

**Subagents spawn subagents.** The tool is `Agent`; no tool named `Task`
exists at any level, and the `Task*` family is task-tracking, not spawning.
Children receive the full `subagent_type` roster, including plugin-scoped
types. Depth past two levels is unverified.

**A child's result never reaches its parent.** The `Agent` tool result is a
launch acknowledgement; the completion notification surfaces in the main
session instead. A parent that needs the text must poll the child transcript.
Design no agent to consume its child's return value.

**`Agent` parameters:** `description`, `prompt`, `subagent_type`, `name`,
`model`, `isolation` (`mode`/`team_name` where exposed). There is no
`max_turns`, no `run_in_background`, no `resume`; the schema is
`additionalProperties: false`, so passing one errors rather than being ignored.
**Resumption is `SendMessage` to the agent's `name`** — which makes naming an
agent at spawn time load-bearing, not cosmetic.

**A declared `tools:` list is not what arrives.** `Agent` is present even when
undeclared. A declared `AskUserQuestion` may be absent while an undeclared
`Artifact` and `ToolSearch` are present. Treat the declaration as a request,
not a contract, and verify before relying on any tool in an agent.

**`Grep` and `Glob` are build-dependent.** Native macOS/Linux builds drop them
from CC 2.1.117 onward, replacing them with search embedded in `Bash`; Windows
and npm-installed builds keep them, and `--tools Grep,Glob` restores them on
native builds (CC 2.1.162). Where they are absent, search runs through `Bash`
(`rg`, `rg --files`) and an agent without `Bash` cannot search at all — so an
agent definition that lists only `Read`/`Write`/`Edit` is search-blind there.

See [[feedback-stale-claims-survive-reference-sweeps]].
