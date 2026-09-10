# Spec-Driven Multi-Agent Development System

A reusable skeleton for building software with a fixed roster of specialized AI agents,
coordinated through a shared set of spec files and a small number of protocols instead of ad-hoc
prompting. A human idea goes in; a sequence of phase-gated agents turns it into a designed,
implemented, reviewed, and deployed system — each agent reading only the files it needs, writing
only the files it owns.

## How it works

The system is organized around four ideas:

- **Context** (`context/`) is the single source of truth for a project — the PRD, architecture,
  API contract, schema, design system, coding standards, and guardrails. Every agent's output is a
  spec file, not a conversation.
- **Agents** (`agents/`) are narrow, reusable roles (Product Manager, Systems Architect, Backend
  Engineer, ...). Each one reads a fixed, small subset of `context/` and produces a fixed output —
  see `agents/README.md` for the full roster and the universal rules every agent follows.
- **Issues** (`issues/`) are the unit of implementable work, broken out by Product Manager once
  Phase 2's specs exist, and claimed — never self-assigned — by whichever agent implements them.
- **Gates** (`gate-checks.md`) are the checkpoints between phases. No phase starts until the
  previous one's exit checklist passes in full, including whichever human sign-offs it requires —
  no agent's own verification can substitute for those.

A Tech Lead agent orchestrates all of this: it's the only agent that writes shared state
(`progress-tracker.md`, `dependency-graph.json`), the only one that grants task claims, and the
one that runs gate verification and spec amendments.

## The four phases

1. **Discovery & Definition** — Product Discovery Agent interviews the human and produces
   `project-overview.md` + `glossary.md`. Exiting requires explicit human PRD approval.
2. **System Design** — Systems Architect, Database Designer, and UI/UX Designer produce
   `architecture.md`, `api-contract.md`, `data-schema.md`, `design-system.md`, and ADRs. Security
   Analyst runs a design-level smell check before the gate closes.
3. **Implementation** — Product Manager turns the design into issues; Backend and Frontend
   Engineers build against the contract; Code Reviewer, QA, and Security Analyst each sign off
   independently — one objecting blocks the gate regardless of the others.
4. **Deployment** — DevOps Engineer builds the path to a running system and a rollback that's
   actually been tested, not just documented. Exiting requires explicit human deploy approval.

See `gate-checks.md` for the exact entry/exit criteria for each phase.

## Design principles

- **Context partitioning** — an agent gets its own definition file plus exactly the `context/`
  files its "Reads" section names, never the whole directory and never another agent's file. This
  keeps token cost down and stops an agent from improvising off specs it was never meant to see.
- **Single-writer rule** — shared state (`progress-tracker.md`, `dependency-graph.json`, and each
  spec file) has exactly one agent authorized to write it. Everyone else proposes changes; the
  owner applies them. This is what prevents silent overwrites when agents run in parallel — see
  `concurrency-protocol.md`.
- **Claims are granted, not taken** — an agent never self-assigns a task. It requests a claim; Tech
  Lead grants or denies it after checking dependencies and Definition of Ready — see
  `handoff-protocol.md` §3.
- **Specs can be wrong, and that's expected** — any agent that finds a spec defect mid-build blocks
  and triggers a versioned correction instead of silently reopening a closed phase or guessing —
  see `spec-amendment-protocol.md`.
- **Failure has a defined path** — timeouts, malformed handoffs, scope creep, and infinite loops
  each have a specified retry/escalation behavior, logged for later review — see
  `agent-failure-protocol.md`.

## Directory map

```
context/                   — single source of truth for THIS project (populate per-project)
context/spec-versions/     — runtime-generated, not part of the copied skeleton: one snapshot
                              folder per spec version (v1.0.0, v1.1.0, ...) plus current.txt,
                              the plain-text pointer to the active version. Tech Lead creates and
                              owns this (see spec-amendment-protocol.md).
context/handoff-log.jsonl  — runtime-generated, append-only log of every handoff (pass/fail),
                              written only by Tech Lead (see handoff-protocol.md §5).
agents/      — agent definition files (reusable across projects, do not edit per-project)
issues/      — task breakdown, populated by Product Manager at the Phase 2→3 transition (needs
               Phase 2's specs to exist first — see agents/product-manager.md)
handoff-protocol.md        — message format + claim/state-update rules
concurrency-protocol.md    — worktree assignment, single-writer rule, merge procedure
agent-failure-protocol.md  — retry/escalation rules + logging requirement
gate-checks.md             — phase entry/exit criteria, including required human sign-offs
spec-amendment-protocol.md — how a spec gets corrected after a phase has closed
```

## Getting started

1. Copy this whole skeleton into your project root.
2. Feed the raw idea to `agents/product-discovery-agent.md` first — everything else traces back to
   the `project-overview.md` it produces.
3. Default to single-agent, sequential mode unless you have a specific, stated reason to
   parallelize (see `agents/tech-lead.md` §"Mode Selection") — most projects don't need twelve
   agents running at once.
4. Give the Tech Lead agent this README and the full `context/` directory. Every other agent gets
   only the files listed in its own "Reads" section in `agents/README.md` — that's the whole point
   of context partitioning; don't defeat it by over-sharing.
