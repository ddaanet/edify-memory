---
name: crosshair-verified-falsification-probe
description: "Don't trust a CrossHair `verified` at face value — probe it with a known-false postcondition to rule out unknown-in-disguise"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8abf3f30-2f33-4c3c-92ba-59902ca0e65c
---

When `edify check` / CrossHair returns **`verified`**, do NOT report it as a
genuine proof without checking it is not an `unknown` masquerading as success.
Because [[verify-loop-direction]] folds `unknown`/timeout into `verified` (D4:
CrossHair has no distinct exit code; a timeout returns 0), a `verified` on a
function with constructs CrossHair reasons about poorly (regex, complex strings,
unbounded loops) may mean "the solver never reached that branch," not "the
postcondition holds."

**The probe (verify the verifier):** temporarily add a postcondition you *know*
is false **and that can only be refuted by exploring the suspect branch**. Run
`edify check` again.
- If CrossHair **refutes** it with a concrete counterexample in that branch →
  the solver does reach the branch → the original `verified` is a genuine
  (bounded) proof. Remove the probe, keep the real contract.
- If CrossHair still says **`verified`** on the known-false probe → it is NOT
  exploring that branch → the original result is `unknown`-in-disguise; report
  it honestly as such, never as verified.

**Budget is a vacuity cause, not just timeout.** A `verified` is only as strong
as the budget that produced it. With no `--timeout`, no `--per_condition_timeout`
reaches CrossHair and it uses its own default — which can be too small to
*synthesize the specific input* an interesting branch needs (e.g. two matching
backticks). The branch never runs, the postcondition holds emptily, you get
`verified`. Remedy: re-run a suspect `verified` at a raised `--timeout` (30 was
enough to flip a vacuous pass to a real refutation in D10).

**Strongest probe = a contract-violating CODE mutation, not just a false
postcondition.** Injecting a realistic bug into the implementation and keeping
the *real* contract tests the exact paths the contract guards; if CrossHair still
says `verified`, the original pass was hollow. A false postcondition tests
reachability; a code mutation tests the contract's actual discriminating power.

**Why:** this is the load-bearing honesty discipline of the formalize loop
(NFR4/NFR5). A `verified` claim is only worth as much as evidence the verifier
actually exercised the logic. Evidence before assertion.

**Worked example (2026-06-15):** contracting `check.py:parse_crosshair_output`
returned `verified`; the regex `exit_code==1` branch was suspect. Probe
`@ensure(lambda exit_code, result: exit_code != 1 or result.findings != ())`
(false: exit 1 with unparseable stdout → empty findings) was refuted with
`parse_crosshair_output(1, '\n', '', target='')` — proving the branch is
explored and the original verdict real.

**Worked example (2026-06-15, D10):** `markdown_inline_fixes.find_inline_code_spans`
returned `verified` at default budget. A fault-injected `start_pos+1` off-by-one
*also* passed `verified` at default budget — hollow. Only `--timeout 30` flipped
it to refuted (`find_inline_code_spans('\x00`\x01`')` → `[(2,4)]`, `line[2]` not
a backtick). The default-budget pass was vacuous because CrossHair never
synthesized two matching backticks. See [[verify-loop-direction]] L7.
