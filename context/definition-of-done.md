# Definition of Done
<!-- Owner: Product Manager, refined by Code Reviewer / QA / Security for their gate criteria. -->

A task is done when ALL are true:
- [ ] Code follows `coding-standards.md`
- [ ] Code Reviewer approved
- [ ] QA: relevant tests pass, including any new contract tests the change requires
- [ ] Security Analyst: no new Critical/High findings introduced
- [ ] Documentation updated if the change affects `api-contract.md`-visible behavior
- [ ] `dependency-graph.json` status updated to `done` by the Tech Lead (never self-set)

If any condition is false: task stays `review` or `blocked` — the agent does not report
`complete` and continue on to the next task.
