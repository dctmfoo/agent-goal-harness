# agent-goal-harness

A template repository for running a **long, autonomous agent goal** (hours to days, across many
context windows) with durable state, two-way human steering, and clean resume after every
context reset.

It generalizes a process that was proven over a multi-day autonomous run: the agent worked
continuously, the human steered it live without stopping it, and every context compaction was a
non-event because the filesystem — not the chat — was the memory.

## The core idea

**The filesystem is the memory; the context window is a cache.**

Everything the agent needs to resume — its position, its next action, in-flight state, pending
instructions, open questions — lives in small, predictable files kept current at every step and
committed to git. A fresh context reads a fixed, short list of files and continues exactly where
the previous one stopped. No long-document re-reads, no reconstruction from chat history.

## What's in the box

| Piece | Role |
|---|---|
| `AGENTS.md` | Thin anchor the agent reads first — pointers, not prose |
| `CHARTER.md` | The goal: mission, the few binding invariants, what success looks like |
| `OPERATING-LOOP.md` | The default working shape — loose on method, binding on bookkeeping |
| `control/` | Live steering: numbered directives (human → agent), heartbeat + state card (agent → human) |
| `inbox/` | Two-way notes for things that genuinely need the human |
| `work/` | One directory + ledger row per unit of work; the truthful record of what happened |
| `findings/` | Durable knowledge and decisions distilled from the work |
| `ADOPTION.md` | Instructions for the agent that adapts this template to a new goal |

## How to use it

1. **Start a session with your coding agent** (Claude Code, Codex, …) in an empty project
   directory, and say something like:

   > Clone https://github.com/dctmfoo/agent-goal-harness as the base for a new project.
   > Read its ADOPTION.md and adopt it for this goal: <describe your goal>.

2. **The agent adopts the template**: it fills in `CHARTER.md` and `AGENTS.md` for your goal,
   names the unit of work, trims what the goal doesn't need, deletes `ADOPTION.md`, and commits.

3. **Review the adopted charter**, then tell the agent to start. From then on you steer by
   dropping `control/directives/D###-*.md` files and monitor via `control/HEARTBEAT.md`,
   `git log`, and `work/LEDGER.md` — without ever interrupting the run.

## Why it works

- **Cheap steering.** A directive is a numbered file; the agent polls the directory listing
  against a cursor at every step boundary. New instructions are noticed in minutes and
  acknowledged with a committed receipt. Old ones are never re-read.
- **Cheap monitoring.** The heartbeat is overwritten, never grows, and is always current.
  `git log --oneline` is a live progress feed because the agent commits per completed step.
- **Resume is designed-in, not exceptional.** The state card always names the current unit, the
  exact next action, and any in-flight environment state. Compaction loses nothing.
- **Truthful records.** Abandoned or half-done work says so in the ledger. The record never
  looks better than reality.

## License

MIT — see `LICENSE`.
