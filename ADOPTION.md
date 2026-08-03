# ADOPTION.md — instructions for the adopting agent

You are an agent adapting this template to a specific goal. The human has given you (or will
give you) a goal description. Your job is to turn this generic harness into that goal's
repository, then delete this file. The **machinery** (control channel, inbox, state card,
heartbeat, resume protocol, commit discipline) ships ready to use — do not redesign it.
The **content** (charter, anchor, unit of work, findings docs) is what you adapt.

## Step 0 — understand the goal

If the human's goal statement leaves any of these unclear, ask before writing:

- What is the mission, in one paragraph? What is the product of the run (knowledge, artifacts,
  a working system, a migrated dataset, …)?
- What environments/systems may the agent touch, and what is the hard safety boundary —
  the things no instruction may override?
- What does "done" or "success at any stopping point" look like?
- What is the natural **unit of work**? (An experiment, a task, a module, a chapter, a
  migration batch…) Some goals have no unit structure at all — that's fine.

## Step 1 — fill in `CHARTER.md`

Replace every `{{PLACEHOLDER}}`. Keep the structure: mission, who the agent is, what the
project is NOT, **binding invariants** (few, hard), scope guidance (loose), success. The
guidance comments in the file tell you what belongs where. Resist adding invariants — every
rule that isn't genuinely binding belongs in scope guidance or nowhere.

## Step 2 — fill in `AGENTS.md`

Replace the placeholders. Keep it a **thin anchor**: pointers and the handful of currently
binding facts, under ~25 lines. It will be updated during the run as directives change standing
behavior; it must never grow into a manual.

## Step 3 — name the unit of work

- Pick the goal's natural term (trial, task, experiment, module, batch, …).
- Rename `work/` if a different name reads better (`trials/`, `tasks/`…), update
  `work/README.md`, `work/LEDGER.md`, and every reference in `AGENTS.md`, `CHARTER.md`,
  `OPERATING-LOOP.md`, and `control/README.md`.
- Adapt `work/UNIT-TEMPLATE.md` to the fields that unit actually needs.
- If the goal has no meaningful unit decomposition, delete `work/` and adapt
  `OPERATING-LOOP.md` to the goal's real rhythm — but keep the step-boundary bookkeeping
  (poll → heartbeat → state → commit) exactly as specified.

## Step 4 — adapt `OPERATING-LOOP.md`

Rewrite the middle steps (pick → do → record → close) to the goal's real working shape. Do not
touch the binding step-boundary obligations or the resume protocol. Keep the loop **loose**:
it describes a default shape, not a script — method, order, and judgment belong to the
executing agent.

## Step 5 — seed `findings/`

Keep `findings/DECISIONS.md`. Add whatever distilled-knowledge documents the goal will need
(a mapping atlas, a playbook, a coverage table…) — or none. Every findings doc must earn its
place: it exists so a later reader learns something durable without replaying the work.

## Step 6 — finish

1. Update `README.md`: replace the template description with a short description of this
   project (keep or trim the "how it works" material as you see fit).
2. Check `.gitignore` fits the goal (secrets, scratch, build output). **Never commit
   credentials or secrets**, regardless of what else changes.
3. Delete this `ADOPTION.md`.
4. Initialize `control/AGENT-STATE.md` and `control/HEARTBEAT.md` from their templates with
   real initial values (`directives_acked: none`, first unit/next action).
5. Commit everything as `adopted: <goal, one line>` — this commit is the run's baseline.
6. Report to the human: the charter summary, the chosen unit of work, anything you trimmed,
   and the open questions from Step 0 you couldn't resolve. The human reviews the charter
   before the run starts.

## What you must NOT do while adopting

- Do not weaken the control protocol, the resume order, the heartbeat/state-card shape, or the
  commit discipline. They are the point of the template.
- Do not pre-write findings, ledger rows, or state that pretends work has happened.
- Do not add speculative machinery (schemas, scripts, CI, extra registries) the goal hasn't
  asked for. The harness stays lean until the work itself earns additions.
