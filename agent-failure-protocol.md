# Agent Failure Protocol

## 1. Failure types & responses

| Failure Type | Detection | Response |
|---|---|---|
| API/tool timeout | No response within configured limit | Retry 3x, exponential backoff (1s, 2s, 4s). Still failing → escalate to Tech Lead as `blocked`. |
| Invalid JSON handoff | Parse fails | Retry 2x with the schema re-stated verbatim in the prompt. Still failing → escalate. |
| Schema violation | Parses but missing required field (`task_id`, `status`, etc.) | Reject, retry once with the specific missing field named. Second failure → escalate. |
| Hallucinated scope (agent did work outside its issue) | Code Reviewer / QA catches at gate check | Reject the whole handoff, not just the extra part. Re-issue the task with the boundary restated. |
| Spec drift (agent used a `spec_version_used` older than `current`) | Tech Lead compares on handoff | If the version diff doesn't touch files this task read, accept. If it does, reject and re-run against current spec. |
| Infinite loop / repeated identical tool calls | Same tool + args called 5x by one agent on one task | Tech Lead halts the agent, escalates. Do not auto-retry a loop. |
| Conflicting outputs from two agents | Detected at merge (see `concurrency-protocol.md` §2) | Tech Lead reviews both, either picks one, requests a revision from one agent, or opens a spec amendment if the conflict traces back to an underspecified contract. |
| Token/output truncation | Output ends mid-JSON | Retry once with a narrower task scope (split the issue if it's genuinely too large for one pass). |

## 2. Escalation path

```
Agent fails → automatic retry (per table above) → still failing →
Tech Lead reviews → Tech Lead can resolve (reassign, re-scope, correct spec)? →
  yes → resume
  no  → human intervention required, conversation/run pauses with a clear summary of what's stuck
```

Tech Lead must never retry past the caps in the table above by itself "just once more" — a
capped retry that keeps failing is a signal the *task or spec* is wrong, not that the agent needs
another attempt.

## 3. Logging requirement (feedback loop)

Every failure — retried, escalated, or resolved — gets one line in `context/handoff-log.jsonl`
(same file as successful handoffs, see `handoff-protocol.md` §5): timestamp, agent, task_id,
failure_type, retry_count, resolution. Without this, recurring failure patterns (e.g. the same
agent always misreading one particular field in `api-contract.md`) are invisible until someone
happens to notice. Review this log at the end of each phase, not just when something breaks live.

## 4. Human sign-off is not optional at these points

Regardless of how well the retry/escalation logic works, a human must explicitly approve:
- PRD approval at the end of Phase 1 (see `gate-checks.md`)
- Any spec version bump to a new **major** version (breaking change)
- Deployment to production (end of Phase 4)

An AI Tech Lead checking its own gate is not an independent check for these three — don't let
automated gate-passing substitute for them.
