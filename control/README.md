# Control channel — live directives, heartbeat, resume (binding protocol)

This directory lets the human (or an assisting agent acting for them) steer and monitor a long
run (hours to days) **without stopping the executing agent**, and lets the agent pick up new
instructions cheaply — no long-document re-reads, no context bloat.

```
control/
  README.md          # this protocol
  directives/        # D###-<slug>.md — numbered instructions FROM the human TO the agent
  AGENT-STATE.md     # the agent's cursor + position; overwritten, never grows
  HEARTBEAT.md       # the agent's live status; overwritten, never grows
```

## Directives (human → agent)

- One instruction = one file `directives/D###-<slug>.md`, numbered sequentially (`D001`,
  `D002`…), a few lines each: what to do differently, starting when, and whether any standing
  doc should be updated to match. Files are **append-only as a set**: never renumber, rewrite,
  or delete an existing directive — supersede it with a newer one ("D007: disregard D005").
- **Precedence:** a newer directive overrides older directives and overrides the standing docs
  (`CHARTER.md`, `OPERATING-LOOP.md`, …) where they conflict — *except* it cannot override the
  charter's safety-boundary invariant.
- If a directive changes something durable, it may instruct the agent to edit the standing
  doc; the agent commits that edit citing the directive id.

## How the agent checks — the cheap poll (binding)

1. Keep a cursor in `AGENT-STATE.md`: `directives_acked: D003` means D001–D003 are processed.
2. A check = **list `control/directives/`** and compare filenames against the cursor. Nothing
   is read unless a number above the cursor exists. Listing a directory costs ~nothing.
3. Read only the new file(s), act or plan the change, advance the cursor, and record the ack
   in **two places**:
   - a one-line entry in `AGENT-STATE.md`'s ack log (last ~10 kept), and
   - **appended to the directive file itself** — add at the bottom:

     ```
     ---
     ## Ack (agent)
     acked: <UTC timestamp> (session N)
     read: yes — <one line: what you understood the directive to require>
     action: <one line: what you did/changed, or "queued: <when it will apply>">
     carried_by: <applied in commit <sha> | ledger unit U### | standing doc <path>>
     ```

     then commit the directive file. The issuer's text above the `---` is immutable; the ack
     and close blocks are the only things an agent may ever add to an existing directive.
     This makes every directive a self-contained, durable receipt: instruction + proof-of-read
     + action, in git. A directive without an ack block is by definition not yet processed —
     the issuer can see at a glance what has and hasn't been picked up.
4. **Acked is not done.** A directive has three states, and only the file shows which:
   - **open** — no ack block;
   - **acked** — ack block present. If `action` is `queued`, `carried_by` names the ledger
     unit that will do the work, and that unit's ledger row names the directive in its
     `Carries` column in the same commit. A queued directive with no carrier row is a
     protocol defect, not a plan;
   - **closed** — a `## Closed (agent)` block at the bottom naming the UTC time and the
     commit or unit that proved it. Applied-now directives close in the ack commit; queued
     ones close in the commit that closes their carrier unit.

   The cursor only says which files have been read. What is owed lives in the ledger's
   `Carries` column and in `HEARTBEAT.md`'s `directives_open` line, which lists every acked
   directive without a close block. A carrier unit cannot be set to `done` while a directive
   it carries is not closed. If a carrier unit is refuted, blocked or abandoned, the directive
   is re-carried by another unit or raised to `inbox/` in the same commit — it is never
   dropped silently.
5. **Check cadence:** at minimum — start of every operating-loop step (not just every unit),
   before and after any operation expected to take >10 minutes, and immediately after any
   restart/resume. During long waits, check while waiting. Target: a new directive is noticed
   within ~10–15 minutes of landing.
6. **Re-check open directives at every poll, not only new ones.** After comparing filenames
   against the cursor, read `directives_open` and confirm each listed directive still has a
   live carrier in the ledger. This is a glance at two lines, not a re-read of the files.
7. Never re-read the standing docs on a poll. Re-read a standing doc only when a directive
   says it changed, or after a context reset (see Resume).

## Heartbeat (agent → human) — overwrite, never append

Rewrite `HEARTBEAT.md` at every operating-loop step boundary and at least every ~15 minutes
during long operations. Keep it under ~20 lines, exactly this shape:

```
# Heartbeat
updated: <UTC timestamp>
unit: <current unit id/slug> (<status>)
step: <operating-loop step + one-line description of what is happening right now>
last_done: <the most recent completed thing, one line>
next: <the immediate next intent, one line>
blockers: <none | one line each, matching open inbox/ items>
directives_cursor: D00X
directives_open: <none | D00X (carried by U###), ...>   # acked, not yet closed
session: <nth context/session of this run, if known>
```

The heartbeat is **status, not history**. History lives in git commits, `work/LEDGER.md`, and
the unit files — that is where a monitor drills down. If the heartbeat and reality ever
disagree, that is a defect: fix the habit, not just the file.

Commit cadence for monitorability: commit at least once per completed operating-loop step
(not only per unit), so `git log` is a live progress feed.

## Resume after context reset (expected, not exceptional)

A long run will outlive any single context window. Design assumption: **the filesystem is the
memory; the context is a cache.** After any reset, a fresh context resumes by reading, in
order:

1. `control/AGENT-STATE.md` (cursor + position + resume pointer)
2. `control/HEARTBEAT.md` (what was in flight)
3. the current unit's file in `work/` (only that one)
4. any directives above the cursor, and the `directives_open` list — each open one must
   still have a carrier row in the ledger
5. `work/LEDGER.md` (skim)

— and only then, standing docs *as needed*. To make this work, `AGENT-STATE.md` must always
name the current unit, the exact next action, and any in-flight environment state (e.g.
"restart of service X issued, not yet verified"). Update it whenever those change, and always
before any operation that could be interrupted. Keep it under ~30 lines; it is a pointer
card, not a journal.

## How the human side monitors

Cheap reads, any time, no interruption: `HEARTBEAT.md` (now), `git log --oneline -10`
(recent), `work/LEDGER.md` (overall), `findings/` (knowledge so far), `inbox/` (questions
waiting on a human). To steer: drop the next `D###` file — the agent notices within its
cadence. To see what is still owed: `HEARTBEAT.md`'s `directives_open` line, or
`grep -L 'Closed (agent)' control/directives/D*.md` for every directive not yet closed.
