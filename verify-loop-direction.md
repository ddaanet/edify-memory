---
name: verify-loop-direction
description: "The concrete first step of Edify's empirical-formal mission — edify check + formalize verify loop (decided 2026-06-08)"
metadata: 
  node_type: memory
  type: project
  originSessionId: 5c285ce3-3504-4e61-a6f3-1b4cf3c2b4ba
  modified: 2026-08-14T12:57:43.656Z
---

Decided 2026-06-08. Edify's mission ("invariant-guided agentic code
generation", per `../ddaanet/drafts/brief-invariant-guided-agent.md`) got its
first concrete artifact designed: an **`edify check` CLI** that wraps
**CrossHair** (symbolic execution + Z3) to verify a Python function against its
**icontract** `@require`/`@ensure` contract, plus a **`formalize` skill** that
drives a propose-contract → check → repair loop with the *in-context* agent
holding intent (and asking the user on ambiguity).

**Load-bearing principle:** separate **validation** (is the contract the right
one? — needs project context + ability to ask; owned by the in-context agent)
from **verification** (does code meet the contract? — deduction, the LLM's weak
spot; offloaded to CrossHair). An isolated API-driven agent is rejected for
exactly this reason — it can't disambiguate intent.

**Backend = CrossHair** (not Nagini, not Lean). Nagini deferred: needs a JVM,
piles permission/separation-logic annotations onto the LLM where it's weakest,
and its counterexample output is experimental. Lean rejected: it verifies Lean,
not Python; its mathlib value is for *proving math*, not verifying Python
behavior; the brief cites Leanstral only as architectural prior art (LLM +
perfect verifier + pass@N), not a tool to adopt.

**Prior art is honest:** LLM-generates-contract + CrossHair-verifies is
*published* (NL2Contract / "Beyond Postconditions", arXiv 2510.12702) — **not
novel**. But it's one-shot, no repair loop, no human-in-the-loop, no shipped
tool. The free niche (confirmed via web search incl. the CrossHair author's own
work): a **CLI/skill that wraps CrossHair in an iterative repair loop with
human-in-the-loop intent disambiguation**. Contribution = engineering + the
ask-on-ambiguity discipline, de-risked by the research.

**Supersedes the requirements-skill rebuild** that [[strategic-pivot]] called
"the seed of the new mission": intent-capture now lives *inside* this loop, so
the old `requirements` decouple/rebuild thread is closed, not pursued.

**Billing note (for the deferred eval harness):** a hand-rolled API-key harness
is both pay-per-token *and* ToS-disallowed on a subscription; the official Agent
SDK / `claude -p` is the blessed plan-credit path (separate Agent-SDK credit
pool as of ~2026-06-15 — verify figures).

Artifacts: spec
`docs/superpowers/specs/2026-06-08-invariant-guided-verify-loop-design.md`
(frozen); plan `docs/superpowers/plans/2026-06-08-edify-check-verify-loop.md`
(frozen, **executed** 2026-06-09 — all 9 TDD tasks committed). The **living
design** that tracks evolving state is
`docs/superpowers/design/invariant-guided-verify-loop.md` (FR/NFR/decisions/
limitations/changelog per [[ddaanet/design-doc-writing]]); it supersedes the
frozen spec where they diverge. As of 2026-06-15 the plumbing is proven on the
seed but the **thesis is unproven** (limitations L5/L6): the repair loop is not
yet shown to beat the paper's ~35–39% one-shot rate, which needs the deferred
eval harness.
