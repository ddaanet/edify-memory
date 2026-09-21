---
name: subagent-write-blocks-report-basenames
description: "subagent `Write` refused with \"Subagents should return findings as text\": built into CC, `.md` basename starting REPORT/SUMMARY/FINDINGS/ANALYSIS, no setting"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 0ad8d7b0-11d4-4cc6-a7b2-afdcff6f57ad
  modified: 2026-09-21T15:53:03.073Z
---

A subagent's `Write` call fails with "Subagents should return findings as
text, not write report files. Include this content in your final response
instead." (errorCode 5) when both hold:

- the caller is a subagent (any type — the check reads `agentId`, nothing else);
- the target's **basename** matches
  `/^(REPORT|SUMMARY|FINDINGS|ANALYSIS).*\.md$/i`.

The directory is not tested: `plans/x/reports/crlf-red.md` writes fine,
`references/report-template.md` is refused. The check sits in the Write tool's
`validateInput`, ahead of the allow/deny permission rules, so no permission
rule lifts it; no setting, env var or agent-frontmatter field references it.
Telemetry event: `tengu_subagent_md_report_blocked`. It is not a hook — the
string appears in no plugin, and searching hooks for it finds nothing.
Verified against CC 2.1.278 by reading the compiled bundle.

**How to apply:** when a dispatch names an output file (see
`shared-claude.md` §Standing defaults, "A dispatch whose output matters names
an output file"), give it a basename that does not start with one of the four
words — `sweep-review.md`, `review-report.md`, not `report-….md` or
`summary.md`. The same goes for production files a subagent must create: name
them around the regex, or have the main session write them. A subagent that
hits the refusal and falls back to a Bash heredoc has bypassed a harness guard
without asking — say in the dispatch that a refused Write is reported, not
worked around.
