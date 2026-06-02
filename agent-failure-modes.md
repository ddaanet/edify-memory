# Agent Failure Modes

Hard-won lessons from agent execution failures. These patterns recur and are expensive to debug.

## Cached Agent Definitions

**Problem:** Agent system prompts are built at session startup and cached. Modifying `.claude/agents/*.md` mid-session has no effect.

**Symptoms:** Agent returns 0 tool uses. High reported tokens but ~2-3s duration (physically impossible without cache). Fabricated file content, test results, commit hashes in output.

**Fix:** Restart session after modifying agent definitions. For testing variants without restart, use `claude -p` to spawn fresh sessions.

**Related:** Reusing plan names for rework (e.g., `handoff-cli-tool` for both original and rework) causes `prepare-runbook.py` to regenerate agents with same names → name cache match → stale context. Iterate plan names (`handoff-cli-tool-v2`) or restart.

## Simulated Tool Output

**Problem:** Agent returns text containing `<tool_call>`, `<function_calls><invoke>` — orchestrator reads fabricated XML as real tool results.

**Detection:** Check CLI output for `(N tool uses)` count. 0 tool uses = entire output is text simulation.

**Evidence:** Three instances in one session: orchestrator accepted fabricated file content, test results, and git commits.

## Sycophantic Test Dismissal

**Problem:** Agent runs scoped tests (single file), gets green. Full suite at commit time shows failures. Agent classifies as "pre-existing" without verifying.

**Fix:** If `just precommit` shows failures not in prior commit, they're regressions. Verify with `git stash && just test && git stash pop`.

## Bare Directive Bypass

**Problem:** "Execute and apply fixes inline then handoff and commit" — agent executes directly without skill invocation. No /inline lifecycle, no corrector dispatch, no baseline verification.

**Fix:** Production edits require skill invocation (`/inline`, `/orchestrate`). Two regressions committed in one day from this pattern.

## Competing Execution Paths

**Problem:** Step N says "implement directly" and Step N+1 says "chain to /inline for corrector gates." Agent executes at Step N, skips quality gates.

**Fix:** Steps within a routing path must not offer competing permissions. Upstream steps must not grant execution authority when downstream provides quality gates.

## Delta-Scoped Reviews

**Problem:** Reviewing only changes since last review. Each round inherits blind spots of all prior rounds.

**Fix:** Full-scope every review. Cost of re-reading unchanged code < cost of accumulating undetected findings.

## Primitive Visibility

**Problem:** Exposing both a primitive (`_worktree rm`) and its wrapping skill in agent context. Agent selects the "simpler" primitive that lacks side effects (session.md updates, validation).

**Fix:** Curate agent context to essential high-level commands only. Primitives exist as fallback but must not be in active context.

## Wrong Corrector Agent

**Problem:** /inline Phase 4a dispatches `superpowers:code-reviewer` (PR review toolkit) for post-execution quality gates. That agent is for PR reviews, not inline execution corrector gates.

**Fix:** /inline review routing says code/tests → `corrector` (subagent_type="corrector"). It's fix-capable and writes reports. `superpowers:code-reviewer` is the wrong tool for inline execution quality gates.

## Worktree Agent Dispatch

**Problem:** Dispatching agents with `git show main:plans/<name>/steps/step-X.md` — worktree doesn't have `main` as resolvable ref. Agent spends 15+ tool calls searching, produces no persisted changes.

**Fix:** (1) Read step content in orchestrator and pass inline, (2) provide absolute path to main worktree copy, or (3) implement directly in orchestrator.

## Incomplete Fix Verification

**Problem:** L1 review agent marks an error-handling fix as "FIXED" by verifying the raise site but not the catch site scope. The fix changes the error type correctly at the throw point, but the CLI handler's except clause wasn't updated to catch the new exception from the calling function.

**Evidence:** RC12 — m-2/m-3 fix changed `_validate_inputs` to raise `CommitInputError` (correct type) but `commit_cmd`'s except clause only caught `CleanFileError`. L1 agent said "FIXED — cli.py:26-27 catches CommitInputError" but that handler covers `parse_commit_input()`, not `commit_pipeline()`.

**Fix:** Exception-based fix verification must trace the full propagation path: raise site → intermediate callers → top-level handler. A correct raise with an uncovered catch is worse than the original (unstructured output instead of wrong-code-but-structured).

## Fix Task Context Gap

**Problem:** Fix tasks route via `/design plans/.../reports/deliverable-review.md`. The report references design requirements by short ID (S-3, H-2) but definitions live in the outline/design document. Fixing agent has symptom description but not the full specification it needs to verify correctness.

**Evidence:** handoff-cli-tool RC9→RC15 trend — fixes address the surface finding but miss the broader contract. The m-2/m-3 fix changed the error type (matches finding) but didn't trace through the CLI handler (requires understanding S-3's full error convention).

**Fix:** Fix task commands should include both the report AND the design document reference. Tracked: `plans/design-context-prerequisite/brief.md`.

## Monolithic Review Convergence

**Problem:** Full-scope review of monolithic deliverable set (6000+ lines, 50+ files) hits steady-state minors. Each pass generates ~8 new minors at the same rate old ones are fixed. 0C/0M holds but minor count never reaches zero.

**Evidence:** handoff-cli-tool RC9→RC15 (7 rounds on minors alone). Three independent subcommands reviewed as one surface. Minor count steady-states rather than converging.

**Fix:** Segment deliverables by sub-problem after design finalization. Each segment (~1000-2000 lines) reviewed independently. Convergence per segment is faster. Applies to review scope, not just execution — the multi-sub-problem splitting pattern.

## Circular Justification in Review

**Problem:** Review finds a CLI feature was simplified away. Review says "functionally safe because the skill handles it." But the skill's legacy logic is the thing the CLI was designed to replace — circular justification.

**Evidence:** RC9→RC11 all carried H-2/H-4 as "documented simplifications." Skill's fallback presence used to justify removing the CLI feature designed to replace it.

**Fix:** When a CLI feature was designed to replace skill logic, check: does the legacy skill logic predate the CLI design? If yes, it's the replacement target, not a safety net.
