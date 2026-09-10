# Glossary

<!-- Owner: Product Discovery Agent, first drafted in Phase 1, refined by any agent that
     introduces a new term — but always via this single file, never redefined locally. -->

## Task
A unit of work an agent can be granted (see `handoff-protocol.md` §3). Fields: ID (issue-XXX),
Title, Description, Status (`backlog | claimed | in_progress | review | done | blocked`), Owner.
Canonical record lives in `dependency-graph.json` + the matching `issues/**/issue-XXX.md`.

## Agent
An AI worker with one role, defined in `agents/<role>.md`, that reads a defined set of `context/`
files and produces a defined output.

## Claim
The Tech-Lead-granted act of an agent taking ownership of a task. Exactly one agent per task at a
time. See `handoff-protocol.md` §3.

## Spec
Any file under `context/` other than `progress-tracker.md` and `handoff-log.jsonl` (which are
run-state, not specification).

## Gate
A checkpoint between phases defined in `gate-checks.md`. A gate "passes" only when every checklist
item is checked and every required human sign-off (if any) is recorded.

---
<!-- EXAMPLE — replace with your project's actual domain terms below this line -->
## Example: "Chef" (from a private-chef marketplace project)
A service provider profile with: verified identity, cuisine specialties, service radius,
availability calendar. Distinct from "Client" (the person booking).
