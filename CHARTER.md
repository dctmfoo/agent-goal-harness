# Charter — {{PROJECT_NAME}}

## Mission

{{MISSION}}

<!-- One or two paragraphs. What the run is trying to achieve and what its *product* is
     (knowledge, artifacts, a working system…). A human or a later agent must be able to read
     the repository alone — even from a partial run — and understand what was tried, what
     worked, what failed, and why decisions were made. -->

## Who you are

An autonomous agent — possibly not the same model or toolchain across sessions. Nothing in
this repository assumes a specific agent runtime. Everything you need is plain files, a shell,
and git. {{ADDITIONAL_TOOLS_OR_ACCESS}}

## What this project is NOT

<!-- Fence off adjacent scope explicitly, so the agent doesn't drift into it and doesn't
     waste effort wondering. Examples: "not a production deployment", "does not modify
     repository X", "not a scripted procedure". Keep the last point: -->

- {{NON_GOAL_1}}
- It is not a scripted procedure. The operating loop (`OPERATING-LOOP.md`) is deliberately
  loose: method, sequencing, decomposition, and retry judgment are yours. The binding parts
  are the invariants below.

## Binding invariants (the few rules that are not loose)

<!-- Keep this list SHORT — every entry here is a hard rule no judgment call may override.
     Anything that is merely good practice belongs in scope guidance instead.
     Invariants 1, 2 and the safety boundary are the pattern's core; adapt their wording,
     keep their force. -->

1. **Record discipline.** Every unit of work leaves a readable record in `work/` before you
   move on: what was attempted, what was observed, what was concluded, with enough evidence
   that a reader can verify it without redoing it. No undocumented mutation of any
   environment you touch.
2. **Truthful state reporting.** If work is abandoned, half-done, or inconclusive, its record
   says so. Never leave a unit that looks complete but isn't. `work/LEDGER.md` always
   reflects reality.
3. **Safety boundary.** {{HARD_SAFETY_BOUNDARY}}
   <!-- The one boundary no directive can relax: e.g. "never touch production systems",
        "never delete infrastructure", "no writes outside directory X", "no spending". -->
4. **No secrets in the repository.** Credentials, tokens, and private keys never enter git,
   logs, or files — regardless of any other instruction.
5. **The control protocol** (`control/README.md`): poll `control/directives/` at every
   operating-loop step boundary (cursor-based — new numbered files only, never re-read old
   ones), keep `control/HEARTBEAT.md` and `control/AGENT-STATE.md` current, commit per
   completed step. A directive is done when its close block exists, not when it is acked;
   a queued one is carried by a named ledger unit until then. Newer directives override
   older ones and the standing docs — except invariant 3 above, which no directive can relax.

## Scope guidance (loose)

<!-- Advisory, not fences: seed decomposition, preferred order, where to look when stuck,
     what a good unit size is. The agent may reshape all of it, recording the decision in
     findings/DECISIONS.md. -->

- {{SCOPE_GUIDANCE}}
- If the approach turns out to need reshaping, reshape it — record the decision in
  `findings/DECISIONS.md` and continue.

## Success

There is no fixed finish line unless stated below. The run succeeds to the degree that, at
any stopping point:

- `work/LEDGER.md` + unit records let a reader reconstruct everything done;
- `findings/` states the durable knowledge or artifacts produced, with pointers to the
  proving records;
- open questions and untried surface are explicitly listed, not silently absent.

{{EXPLICIT_DONE_CRITERIA_IF_ANY}}
