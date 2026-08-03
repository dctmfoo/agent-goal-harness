# ADOPTION.md — instructions for the adopting agent

You are an agent adapting this template to a specific goal. The human has given you (or will
give you) a goal description. Your job is to turn this generic harness into that goal's
repository, then delete this file. The **machinery** (control channel, inbox, state card,
heartbeat, resume protocol, commit discipline) ships ready to use — do not redesign it.
The **content** (charter, anchor, unit of work, findings docs) is what you adapt.

## First: which adoption mode?

- **Fresh repo** — the goal gets its own new repository, built from this template. Follow
  Steps 0–6 as written.
- **Existing repo** — the human already has a repository and wants the harness added to run
  a goal inside it. Their repository is theirs: adopt **minimally and additively**. Follow
  Steps 0–6 with the "Existing repo" adjustments below each step, and read the
  "Existing-repo rules" section first.
- **New goal, already-adopted repo** — the repository already contains this harness from a
  previous, finished goal. Do not re-overlay or duplicate anything: follow the
  "New goal, same repo" section instead of Steps 0–6's file creation (Steps 0–2 and 4 still
  apply as *rewrites* of the existing files).

**The mode is the human's decision — confirm it before writing anything.** If they haven't
stated it, look at where you are and **propose**: a non-empty repository with its own history,
code, or docs suggests existing-repo mode; an empty or near-empty directory suggests a fresh
repo. State your proposal in one line ("This looks like your existing project — I'd adopt in
existing-repo mode: everything under `harness/`, nothing else touched. OK, or new repo
instead?") and wait for their answer. Never restructure someone's repository on an inference.

## Existing-repo rules (minimal overlay)

1. **One directory.** Place the entire harness under a single top-level directory, `harness/`
   by default (another name if the human prefers): `harness/CHARTER.md`,
   `harness/OPERATING-LOOP.md`, `harness/control/`, `harness/inbox/`, `harness/work/`,
   `harness/findings/`. Update every internal path reference accordingly.
2. **Touch at most one existing file.** If the repo has an agent anchor (`AGENTS.md`,
   `CLAUDE.md`, or equivalent), append a short pointer block (≤6 lines): the goal in one
   line, "harness protocol in `harness/` is binding for this run", and the resume read
   order. If there is no anchor, create a thin `AGENTS.md` from this template's version
   with paths pointed at `harness/`. That is the only file outside `harness/` you create
   or modify — not their `README.md`, `LICENSE`, CI, code, or structure.
3. **`.gitignore` additions only if needed** (e.g. harness scratch paths), appended, never
   rewritten.
4. **Write a short `harness/README.md`** as the harness entry point: the goal in a
   paragraph, a map of the harness files, and a pointer to the charter. The kickoff prompt
   will name this file as the entry doc.
5. **Do not reorganize, rename, or "improve" anything that was already there.** If the
   goal genuinely requires changing existing project files, that is run work for the
   executing agent under the charter — not adoption work.
6. Commit the overlay as a single commit; do not mix it with any other change to the repo.

In existing-repo mode, read every root-relative path in Steps 1–5 (`CHARTER.md`, `control/`,
`work/`, …) as living under `harness/`.

## New goal, same repo (re-adoption)

A finished run leaves two different kinds of material behind, and they are treated
differently:

- **The run's output is not run residue.** Code, documents, data, or other artifacts the
  previous goal produced are now part of the project — they ship, they get maintained, they
  stay exactly where they are. Archiving a run must never move, rename, or "clean up" the
  goal's output.
- **The harness control state is per-goal.** Charter, ledger, directives, inbox, state card,
  heartbeat — these described the previous run and are reset for the new one.

Git history already is the archive: the previous run committed every loop step, so nothing
needs to move to be preserved. **Do not relocate old harness files into an archive
directory** — that churns history, breaks the ledger's relative references, and risks
dragging goal output along with it.

The procedure, all in place:

1. **Confirm the previous run is actually finished.** Read `control/AGENT-STATE.md`, the
   ledger, and any unresolved `inbox/` items. If the old run looks mid-flight or has open
   blocked items, stop and ask the human whether it is abandoned, done, or to be resumed —
   never overwrite a live run's state on an inference.
2. **Ensure the finished run is tagged.** The executing agent tags its own completion
   (OPERATING-LOOP "Stopping"), so a `run-<n>-<goal-slug>` tag usually already exists —
   verify with `git tag -l 'run-*'`. If it's missing, tag the run's final commit now:
   `git tag run-<n>-<goal-slug>` (next free `n`). The tag is the one pointer to the complete
   previous run; anything from it is recoverable later with `git show run-<n>-<slug>:<path>`.
3. **Rewrite the mutable files in place** for the new goal, following Steps 0–2 and 4:
   new `CHARTER.md` and anchor pointer, truncated `work/LEDGER.md` (header only), emptied
   `control/directives/` and `inbox/` (including `resolved/`), and `control/AGENT-STATE.md`
   / `control/HEARTBEAT.md` re-initialized from their templates. Revisit the unit of work
   (Step 3) — a new goal may have a different natural unit.
4. **Leave `findings/` accumulating.** Decisions and distilled knowledge from the previous
   run document output that is still live in the project; they are more valuable to the new
   run, not archive material. Only the human may decide a findings doc is dead weight.
5. Commit the reset as a single commit, `adopted: <new goal, one line>` — the new run's
   baseline — then finish with Step 6's report and kickoff prompt as usual.

The working tree always shows exactly one thing: the current goal.

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
   project (keep or trim the "how it works" material as you see fit). *Existing repo:* leave
   their `README.md` alone; write `harness/README.md` instead (Existing-repo rule 4).
2. Check `.gitignore` fits the goal (secrets, scratch, build output). **Never commit
   credentials or secrets**, regardless of what else changes.
3. Delete this `ADOPTION.md`. *Existing repo:* don't copy it in at all — it stays behind in
   the template clone.
4. Initialize `control/AGENT-STATE.md` and `control/HEARTBEAT.md` from their templates with
   real initial values (`directives_acked: none`, first unit/next action).
5. Commit everything as `adopted: <goal, one line>` — this commit is the run's baseline.
6. Report to the human: the charter summary, the chosen unit of work, anything you trimmed,
   and the open questions from Step 0 you couldn't resolve. The human reviews the charter
   before the run starts.
7. End your report with the **kickoff prompt** for the human to copy-paste into the executing
   agent (Codex, Claude Code, …) when they are ready to start the run. The prompt is a
   constant — the actual goal lives in the repository files, so the prompt itself never
   carries goal-specific content. Substitute only the project name, absolute path, and entry
   doc (`README.md` for a fresh repo; `harness/README.md` for an existing repo):

   ```
   /goal You are the executing agent for <project name> at <absolute repo path>. Read its
   <entry doc> and follow the doc set from there — the charter's invariants and the control
   protocol (directives, heartbeat, state) are binding from your first action; everything
   else is your judgment. Begin at OPERATING-LOOP step 0. This will be a long run: keep the
   heartbeat, ledger, and commits current so it can be monitored and resumed at any point.
   ```

   Do not add goal-specific instructions, phases, tool restrictions, or plugin rules to this
   prompt. If something feels like it belongs in the kickoff prompt, it belongs in the
   charter, the anchor, or the operating loop instead.

## What you must NOT do while adopting

- Do not weaken the control protocol, the resume order, the heartbeat/state-card shape, or the
  commit discipline. They are the point of the template.
- Do not pre-write findings, ledger rows, or state that pretends work has happened.
- Do not add speculative machinery (schemas, scripts, CI, extra registries) the goal hasn't
  asked for. The harness stays lean until the work itself earns additions.
