# Spec-Driven Multi-Agent Development System

**Version:** 2.0.0
**Status:** Production skeleton — populate `context/` per project, agents are ready to use as-is.

## What changed from v1.0.0

This revision closes six gaps found in review:

| # | Problem in v1.0.0 | Fix in v2.0.0 |
|---|---|---|
| 1 | No rule for who writes shared state → silent overwrites when agents run in parallel | **Single-writer rule**: only Tech Lead writes `progress-tracker.md` and `dependency-graph.json`. See `concurrency-protocol.md`. |
| 2 | Task "claiming" had no atomic mechanism → two agents could claim the same issue | Claims are **granted, not taken**. Agent requests via handoff JSON, Tech Lead grants by writing the status. See `handoff-protocol.md` §3. |
| 3 | Dependency data duplicated in `dependency-graph.json` AND each issue file | `dependency-graph.json` is now the **only** place dependencies live. Issue files reference an ID, nothing else. |
| 4 | Gates were one-directional; no path for "the spec was wrong" discovered mid-build | New `spec-amendment-protocol.md` — any agent can trigger a versioned spec correction without breaking the phase model. |
| 5 | `environment.md` mixed variable names with real secret values | Split: `environment.md` lists names/purposes only (safe to put in any agent's context). Real values live in `.env`, which **no agent ever reads**. |
| 6 | No feedback loop on whether the system itself is working | `agent-failure-protocol.md` now includes an eval/logging requirement: every handoff is logged pass/fail for later review. |

## Directory map

```
context/     — single source of truth for THIS project (populate per-project)
agents/      — agent definition files (reusable across projects, do not edit per-project)
issues/      — task breakdown, populated by Product Manager during Phase 1
handoff-protocol.md        — message format + claim/state-update rules
concurrency-protocol.md    — worktree assignment, single-writer rule, merge procedure
agent-failure-protocol.md  — retry/escalation rules + logging requirement
gate-checks.md             — phase entry/exit criteria, including required human sign-offs
spec-amendment-protocol.md — how a spec gets corrected after a phase has closed
```

## How to use this

1. Copy this whole skeleton into your project root.
2. Start in **single-agent mode** (see `agents/tech-lead.md` §"Mode Selection") unless you have a
   specific, stated reason to parallelize — most projects don't need 12 agents running at once.
3. Feed the raw idea to `agents/product-discovery-agent.md` first. Everything else follows from
   `context/project-overview.md`, which it produces.
4. The Tech Lead agent is the only agent that should be given this README and the full `context/`
   directory. Every other agent gets only the files listed in its own "Reads" section — that's the
   whole point of context partitioning; don't defeat it by over-sharing.
