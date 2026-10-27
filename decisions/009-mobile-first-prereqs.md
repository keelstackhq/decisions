# D-009: Mobile-first owner; human prerequisites are front-loaded

- Date: 2026-10-27
- Scope: all
- Status: accepted
- Supersedes: —

## Context

The owner works mainly from a phone (GitHub app, claude.ai). On 2026-10-27 the owner required
that an autonomous run must not stop halfway to wait for them: "if a human starts a new request,
and there are any tasks to do/prepare that the agent cannot do, this must be handled in the very
beginning, so the agent can then finish the work without any dependency to a human." Until then,
secrets, repo settings, accounts and approvals surfaced one at a time, mid-run.

## Decision

- When a request enters (bosun `intake`, `plan import`/`amend`, or an orchestrator starting work in
  any repo), the orchestrator finds every human-only step first and asks for them in one checklist:
  secrets and environments, repo settings, external accounts (npm, crates.io, CircleCI, DNS),
  bootstrap publishes, tokens and key enrolment, and which tasks will need an owner approval at
  merge. In bosun repos the plan's `## Prerequisites` section and `bosun prereqs --post` produce
  it (one issue with tappable boxes plus a short chat text); elsewhere the orchestrator writes the
  same list in one message.
- Each item says who does it, the exact steps (copy-paste ready), whether it can be done on a
  phone or needs a computer (computer items first), how the agent checks it is done, and which
  tasks it blocks.
- Only the tasks an open item blocks wait; all other work proceeds. Agents never stop mid-run to
  ask for a setup step: an agent that finds an undeclared one adds it to the plan (with `blocks:`
  its task), releases the task, and takes other work; the orchestrator relays the updated list
  once.
- Owner approvals at merge are batched: the orchestrator schedules approval-needing tasks so their
  PRs are ready together and relays one message ("approve 12 15 17"), answerable in chat (signed
  approvals); classes the owner delegates go to the delegate key, not the owner.
- Every owner step has a phone path (GitHub app or mobile web, claude.ai chat, npmjs.com) or is
  marked "needs a computer".

## Consequences

- Orchestrators in every repo start a request with the checklist, not with spawning agents.
- bosun: `## Prerequisites` in plans, `bosun prereqs`, tasks held `planned` while a prerequisite
  blocks them, `bosun approve --list` gives one batched message (keelstackhq/bosun, 2026-10-27).
- A run that stops mid-way on a missing secret or setting is a planning bug: record a lesson.
