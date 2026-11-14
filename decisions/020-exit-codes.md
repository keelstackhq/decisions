# D-020: one exit-code table across the CLIs

- Date: 2026-11-14
- Scope: all
- Status: accepted
- Supersedes: —

## Context

The stack review found that exit code 2 means "usage error" in bosun, splice and berth, but
"human step pending" in hoist (which exits 3 on usage errors). An agent wrapper that stops for a
human on exit 2 does the wrong thing on a berth typo. Renumbering now would break callers.

## Decision

- Shared meaning for every keelstackhq CLI: 0 ok · 1 drift, refusal or failed check · 2 usage ·
  3 config, environment or API error (the message says how to fix it). Codes 4 and up are
  tool-specific; "needs a human" is 5 (as in bosun).
- hoist is the one exception until its 1.0: 2 = human step pending, 3 = usage. At 1.0 it moves
  to the shared table (usage 2, human step 5) with a changelog entry.
- Agents never branch on a bare exit code across tools: they read the tool's `--json` object
  (`ok`, `exit_code`, `error.fix`, `human_steps`/`next`) and its README table.
- New CLIs follow the shared table from their first release.

## Consequences

No code change today. hoist 1.0 carries the renumbering. Spec and README exit-code sections link
this decision.
