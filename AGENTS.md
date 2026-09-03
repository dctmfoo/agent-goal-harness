Always apply the `unslop` skill (invoke via `/unslop`) to all prose you write: docs, code comments, commit messages, PR descriptions, and replies.

# {{PROJECT_NAME}} agent anchor

<!-- Thin anchor: pointers + currently binding facts only. Keep under ~25 lines.
     Update it when a directive changes standing behavior; never let it grow into a manual. -->

- **Goal:** {{ONE_LINE_MISSION}}
- Start/resume at `control/AGENT-STATE.md`, then `control/HEARTBEAT.md`, the current unit's
  file in `work/`, any unacked directives, and `work/LEDGER.md`.
- The control protocol is binding: `control/README.md`. Poll `control/directives/` at every
  operating-loop step boundary and around any operation expected to exceed ten minutes.
- Charter invariants are binding: `CHARTER.md`.
- The operating loop's shape is in `OPERATING-LOOP.md` — method and judgment are yours;
  the step-boundary bookkeeping is not optional.
- Keep `control/HEARTBEAT.md`, `control/AGENT-STATE.md`, `work/LEDGER.md`, and commits
  current. Commit at least once per completed step.
- Questions only a human can resolve go to `inbox/` per its README; then keep working on
  something else.
- Distill durable lessons into `findings/` as you go, not retrospectively.
- {{GOAL_SPECIFIC_BINDING_FACT_1}}
- {{GOAL_SPECIFIC_BINDING_FACT_2}}
