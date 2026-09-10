# Concurrency Protocol

Governs how multiple agents touch the filesystem at the same time without silently overwriting
each other. Read by: Tech Lead (owns this), DevOps Engineer (sets it up).

## 1. Isolation unit: git worktree per active task

Every task that is `claimed` gets its own git worktree, branched from `main` at the point the
claim was granted:

```
project-root/                    ← main worktree, main branch — nobody edits this directly
project-root-issue-002/          ← Backend Engineer, branch task/issue-002
project-root-issue-010/          ← Frontend Engineer, branch task/issue-010
```

Rules:
- An agent only ever edits files inside its own worktree. It never touches `project-root/`
  directly, and never touches another task's worktree.
- `context/` is read-only from inside a task worktree except for the two files the owning agent is
  allowed to write directly to its own worktree's copy (its code, its tests). `progress-tracker.md`
  and `dependency-graph.json` are edited only in `project-root/` by the Tech Lead (rule 2 of
  `handoff-protocol.md`) — an agent's worktree copy of these is for reading only and is discarded
  at merge time, never merged.
- DevOps Engineer is responsible for the actual `git worktree add` / `git worktree remove`
  commands, triggered by the Tech Lead's claim grant and completion events.

## 2. Merge sequencing

1. Agent finishes, sends `status: "complete"` or `needs_review`.
2. Tech Lead applies the state update to `main`'s `progress-tracker.md` / `dependency-graph.json`.
3. Code Reviewer (and QA / Security if the gate requires it) review the worktree branch, not a
   merged copy.
4. On approval, DevOps Engineer merges `task/issue-XXX` into `main` and removes the worktree.
5. Merge conflicts at this step are a normal git merge conflict, resolved by whichever agent owns
   the conflicting file (never auto-resolved by DevOps). If neither owns it cleanly (e.g. two
   backend tasks both touched the same migration file), escalate to Tech Lead — this usually means
   `dependency-graph.json` under-specified a dependency between the two tasks and needs a
   correction (see `spec-amendment-protocol.md`).

## 3. What does NOT need a worktree

Read-only agents that produce a report rather than code changes — Security Analyst reviewing for
sign-off, QA writing a bug report against existing code — read directly from `main` and never
write source files. Their output is their handoff JSON plus, where applicable, files under
`issues/` (bug reports), not the codebase itself.

## 4. Parallelism limits

Cap concurrent worktrees at the number of *independent* tasks in `dependency-graph.json` (tasks
with no unmet `depends_on`), not the number of agent roles. Running six agents in parallel when
only two tasks are actually unblocked just produces five idle agents and one bottlenecked Tech
Lead — check the graph before spawning, don't spawn one of every role by default.
