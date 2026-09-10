# Handoff Protocol

Every agent invocation ends with exactly one JSON object. No prose outside the JSON in the final
message. This is the only channel agents use to talk to each other — there is no shared memory
beyond the files in `context/`, and files are only ever changed via the rules below.

## 1. Message schema

```json
{
  "agent": "backend-engineer",
  "task_id": "issue-002",
  "spec_version_used": "v1.0.0",
  "status": "complete | blocked | needs_review | claim_request",
  "output": {
    "files_written": ["src/api/tasks.py", "tests/test_tasks.py"],
    "summary": "One or two sentences, not a changelog."
  },
  "state_updates": [
    { "op": "set_status", "target": "issue-002", "value": "review" }
  ],
  "dependencies_met": ["issue-001"],
  "blocked_reason": null,
  "notes": ""
}
```

Field rules:
- `spec_version_used` — the `current` version of `context/spec-versions/` at the time the agent
  started. If this no longer matches `current` when the Tech Lead processes the handoff, the Tech
  Lead must check whether the change affects this task before accepting the output (see
  `spec-amendment-protocol.md`).
- `state_updates` — **the only way** an agent affects `progress-tracker.md` or
  `dependency-graph.json`. Agents never write these files directly (see rule 2). Valid `op` values:
  `create_task`, `set_status`, `add_blocker`, `add_changelog_entry`. Anything else is rejected by
  the Tech Lead.
  - `create_task` — proposes a brand-new entry in `dependency-graph.json`. Used almost exclusively
    by Product Manager when turning `project-overview.md` into issues. `target` is the new
    `task_id`; `value` is the full task object matching `dependency-graph.schema.json`'s shape
    (`title`, `domain`, `status: "backlog"`, `owner: null`, `depends_on`, `blocks`). Tech Lead
    rejects it if the resulting graph would contain a cycle (topological sort fails) or if
    `task_id` already exists.
- `blocked_reason` — required and non-null when `status` is `blocked`. One sentence, specific
  enough that the Tech Lead doesn't have to ask a follow-up.

## 2. Single-writer rule (fixes silent overwrite failures)

Only the **Tech Lead** ever writes to:
- `context/progress-tracker.md`
- `context/dependency-graph.json`

Every other agent's edits to these two files, if it makes any, are ignored. All other agents
propose changes via `state_updates` in their handoff JSON. The Tech Lead applies proposals
serially, one handoff at a time, even when multiple agents finished "in parallel" — parallel
execution means parallel *compute*, not parallel *writes*. This removes the race condition where
two simultaneous writers silently erase each other's changes.

All other files (`api-contract.md`, `data-schema.md`, source code, tests) are written directly by
the agent responsible for them, inside that agent's assigned git worktree — see
`concurrency-protocol.md`.

## 3. Task claiming is granted, not taken

An agent never sets its own task to `in_progress`. The sequence is:

1. Agent sends `status: "claim_request"` with `task_id` set.
2. Tech Lead checks `dependency-graph.json`: task exists, status is `backlog`, all `depends_on`
   entries have `status: "done"`.
3. If valid, Tech Lead applies the state update (`status: "claimed"`, `owner: <agent>`) and returns
   confirmation. Only then may the agent begin work.
4. If another claim_request for the same task arrives before step 3 completes, the second is
   rejected with `blocked_reason: "already claimed"`.

This is intentionally centralized — a single serial arbiter is simpler and cheaper than a
distributed lock, and at agent-team scale (single digit agents per project) it is not a
bottleneck.

## 4. Status codes

- `complete` — task finished, meets its issue's acceptance criteria.
- `blocked` — cannot proceed; `blocked_reason` explains why; Tech Lead resolves per
  `agent-failure-protocol.md`.
- `needs_review` — done but requires Code Reviewer / QA / Security sign-off before `complete`.
- `claim_request` — see rule 3.

## 5. Logging (feedback loop)

Every handoff, once processed by the Tech Lead, is appended as one line to
`context/handoff-log.jsonl` (append-only, Tech Lead is the only writer): timestamp, agent, task_id,
status, and whether the Tech Lead accepted or rejected the state update. This is the raw material
for later auditing whether the system itself is producing good outcomes — it is not read by
agents during normal operation.
