# Operating loop (loose — the shape, not a script)

You own method, order, and judgment. This is the default shape; deviate whenever the work
teaches you something that makes deviation smarter, and record the deviation in
`findings/DECISIONS.md` when it's structural.

**At every step boundary below (binding, from `control/README.md`):** poll
`control/directives/` against your cursor, confirm every directive in `directives_open`
still has a live carrier unit in the ledger, rewrite `control/HEARTBEAT.md`, update
`control/AGENT-STATE.md` if your position or in-flight state changed, and commit completed
work. This costs seconds and is what keeps a long run steerable and resumable.
At each poll, also reconcile open `inbox/` items against their `moot if:` condition,
resolving stale items and keeping heartbeat blockers exact.

## 0. Orient (once per run/session)

- **Fresh context resuming a run in progress?** Follow the resume order in
  `control/README.md` (state → heartbeat → current unit → unacked directives → ledger) and
  skip straight to where the run left off — do not re-read the full doc set.
- First run: read `CHARTER.md`, `AGENTS.md`, `control/README.md`, and skim `work/LEDGER.md`
  and `findings/`. Check `inbox/` and `control/directives/` for anything already waiting.

<!-- If the goal has a mandatory first gate (access proof, environment baseline, data
     snapshot), state it here: "No unit opens until the gate passes." -->

## 1. Pick the next unit

{{HOW_TO_PICK}}

<!-- Where the next unit comes from: a seed inventory in the charter, coverage judgment,
     questions opened by earlier units. State a preferred order if one exists. -->

Open the unit's file in `work/` (from `work/UNIT-TEMPLATE.md`) and its ledger row
(`planned`) before touching anything. Consult `findings/` for applicable priors. A unit
that carries a queued directive names it in the row's `Carries` column and in the unit
file's `carries:` line; the directive's requirements are part of the unit's definition of
done, so re-read that directive when the unit opens.

## 2. Do the work

{{THE_WORK}}

<!-- The goal-specific middle: build, investigate, migrate, write, measure… Log in the
     unit's file as you go, not retrospectively. "Deployed" is not "working" — prove the
     outcome, don't assume it. -->

## 3. Record the outcome

Write what was observed and concluded into the unit's file with its evidence. Set the ledger
status honestly (`done`, `refuted`, `blocked`, `abandoned` — a strong negative is a
first-class result). Distill any durable, reusable lesson into `findings/` in the same close
commit. If the unit carries a directive, append its `## Closed (agent)` block in that same
commit, or, when the unit ends `refuted`, `blocked` or `abandoned`, re-carry the directive on
another unit or raise it to `inbox/` — a carried directive never ends with its unit
silently.

## 4. Loop — or stall productively

When a unit stalls: consult documentation and priors, form a new hypothesis, retry. If it
stays stuck, mark it `blocked` with the precise blocker and move on — don't grind. If a
question needs the human (a scope call, an action beyond your boundary), write it to
`inbox/` per its README and pick a different unit.

## Scratch space

`tmp/` is the only scratch home — gitignored, never committed, nothing citable lives there.
Move anything worth keeping to a tracked location before citing it. Prune at unit close.

## Stopping

Any stopping point must leave: ledger truthful, the in-progress unit's file honest about
where it stands, every acked directive either closed or carried by a live ledger row, loose
ends recorded, everything committed. The next reader — human or
agent — starts at step 0 and loses nothing.

**When the goal itself is complete** (the charter's success condition is met, or the human
declares the run closed): make the final state/heartbeat/ledger commit, then tag it —
`git tag run-<n>-<goal-slug>` (next free `n`; push the tag if the repo has a remote). The
tag is the run's one durable pointer; a later goal in this repository resets the harness
files in place against it (see the template's ADOPTION.md, "New goal, same repo"). Tag
only real completion — never a mid-run stop.
