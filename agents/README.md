# Agents Directory

Twelve agent definitions. Each file is self-contained: give an agent only its own file plus the
exact `context/` files listed in its "Reads" section — not the whole `context/` directory, and not
another agent's file. That's what makes context partitioning actually save tokens.

## Universal rules (apply to every agent, not restated in each file)

1. **Output**: every invocation ends in one `handoff-protocol.md`-schema JSON object. No prose
   after it.
2. **State**: never write `progress-tracker.md` or `dependency-graph.json` directly. Propose via
   `state_updates`. See `handoff-protocol.md` §2.
3. **Claiming**: never self-assign a task. Request via `status: "claim_request"`. See
   `handoff-protocol.md` §3.
4. **Scope**: touch only files inside your own git worktree (`concurrency-protocol.md` §1). Doing
   work outside your issue's stated scope, even if "helpful," is treated as a failure at Code
   Review, not a bonus.
5. **Blocking**: if a spec you need is missing, wrong, or ambiguous, output `status: "blocked"`
   with a specific `blocked_reason` and stop. Never guess and proceed — a wrong guess costs more
   than a blocked task. See `spec-amendment-protocol.md`.
6. **Mode selection** (owned by Tech Lead, relevant to all): don't assume every role needs its own
   invocation. For small tasks, running Systems Architect + Database Designer + UI/UX Designer as
   one broader "Design" pass, or Code Reviewer + QA + Security as one "Verification" pass, is
   often cheaper and just as correct as 12 separate agents — see `tech-lead.md` §"Mode Selection."
   Default to fewer, not more, unless there's a stated reason (true need for parallel throughput,
   or a role that genuinely benefits from a distinct, narrow persona).

## Roster

| Agent | Phase | Reads (in addition to its own issue) | Writes |
|---|---|---|---|
| product-discovery-agent | 1 | — (raw idea only) | project-overview.md, glossary.md |
| product-manager | 2→3 transition | project-overview.md, glossary.md, architecture.md, api-contract.md, data-schema.md, design-system.md, guardrails.md, definition-of-ready.md, definition-of-done.md | issues/, story list |
| tech-lead | all | everything | progress-tracker.md, dependency-graph.json (exclusively) |
| systems-architect | 2 | project-overview.md, guardrails.md, environment.md | architecture.md, api-contract.md, adr/, guardrails.md (sole writer) |
| database-designer | 2 | project-overview.md, architecture.md, api-contract.md | data-schema.md |
| ui-ux-designer | 2 | project-overview.md | design-system.md |
| backend-engineer | 3 | api-contract.md, data-schema.md, coding-standards.md, guardrails.md | source + tests |
| frontend-engineer | 3 | api-contract.md, design-system.md, coding-standards.md | source + tests |
| devops-engineer | 3–4 | architecture.md, environment.md, coding-standards.md | Docker/CI config |
| code-reviewer | 3 | coding-standards.md, guardrails.md, the diff | review verdict |
| qa-engineer | 3 | testing-guidelines.md, api-contract.md, the code | tests, bug reports |
| security-analyst | 2, 3 | guardrails.md, architecture.md, the code | security report |
