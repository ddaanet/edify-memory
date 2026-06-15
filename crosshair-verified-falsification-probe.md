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

**Why:** this is the load-bearing honesty discipline of the formalize loop
(NFR4/NFR5). A `verified` claim is only worth as much as evidence the verifier
actually exercised the logic. Evidence before assertion.

**Worked example (2026-06-15):** contracting `check.py:parse_crosshair_output`
returned `verified`; the regex `exit_code==1` branch was suspect. Probe
`@ensure(lambda exit_code, result: exit_code != 1 or result.findings != ())`
(false: exit 1 with unparseable stdout → empty findings) was refuted with
`parse_crosshair_output(1, '\n', '', target='')` — proving the branch is
explored and the original verdict real.
