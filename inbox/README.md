# Inbox

Two-way notes between the human and the executing agent, as dated Markdown files
(`YYYY-MM-DD-<slug>.md`). Empty inbox = nothing pending.

An agent-to-human item belongs here only when all three conditions hold:

1. the current operating-loop step cannot advance through any charter-compliant self-serve
   action;
2. the unblock requires a human-only action (physical, account, purchase, approval) or a
   charter/scope decision; and
3. the item cites the attempted or prohibited self-serve paths.

The moment that state becomes true, put `blocked_since: <UTC timestamp>` on the matching
heartbeat blocker. At every heartbeat rewrite, raise an item once the same obstacle has
remained blocked for more than 60 wall-clock minutes even if exhaustion is incomplete,
stating what remains untried.

Every item requests exactly one action, states the state the human should leave behind, and
includes a `moot if:` condition. At every directive poll, re-check open items: when satisfied
or moot, append a UTC resolution footer and move the file to `resolved/`.

Every open inbox filename must appear in the heartbeat `blockers:` line, and every heartbeat
blocker that meets the raise bar must have a matching open item. Slower work, an inelegant
path, or a running build/deploy/propagation wait belongs in the unit file or heartbeat, not
here.

- **Human → agent:** scope nudges, environment changes, answers to open questions. (For
  instructions that change how the agent works, prefer a numbered directive in
  `control/directives/` — the inbox is for conversation, directives are for steering.)
- **Agent → human:** current human-only blockers meeting the bar above.
