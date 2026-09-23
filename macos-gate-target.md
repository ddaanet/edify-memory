---
name: macos-gate-target
description: "macOS is a target for edify's gates and scripts — no macOS CI, my human partner runs the suite there manually now and then; write every shell edit under shell-scripting:shell-gotchas and state any residual BSD/macOS bound in a comment"
metadata:
  type: project
---

macOS is a target platform for edify's shell gates and scripts. The plugins are
public and adoption is wanted, so a Linux-only shortcut in a script is a defect
even though this box is Linux.

**Why:** my human partner's ruling, given when the `wc` padding guard in
`scripts/check_line_limits.sh` was questioned as never run on macOS. There is
no macOS CI; my human partner runs the suite on macOS manually, periodically.

**How to apply:** load `shell-scripting:shell-gotchas` before writing or editing
any shell (`.sh`, justfile recipes, hooks, bats) — it covers the GNU/BSD
divergences, bash 3.2 limits and hook-environment traps. Where a claim about
BSD behaviour cannot be verified here, pin robustness in a test as
`tests/line_limits_helpers.py`'s padded-`wc` shim does and state the residual
bound in a comment rather than implying coverage. A pending manual macOS run
settles such comments as verified or deleted. See [[line-caps]] for the gate
the padding guard belongs to.
