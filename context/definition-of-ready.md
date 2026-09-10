# Definition of Ready
<!-- Owner: Product Manager. Applied by Tech Lead before granting any claim. -->

A task is ready when ALL are true:
- [ ] Clear title and description
- [ ] Definition of Done stated (see `definition-of-done.md` + the issue's own criteria)
- [ ] All `depends_on` entries in `dependency-graph.json` have `status: "done"`
- [ ] Every spec file the task's agent needs to read exists and is complete (no placeholder text)
- [ ] Task does not conflict with an already-claimed task on the same file/resource

If any condition is false: the task stays `backlog`. The Tech Lead does not grant the claim, and
explains which condition failed — this is a normal state, not an error.
