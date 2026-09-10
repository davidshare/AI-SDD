# Issues Directory

Organized by domain: `backend/`, `frontend/`, `integration/`, `devops/`.

Each issue file describes the work and its acceptance criteria. It does **not** duplicate
dependency relationships — those live only in `context/dependency-graph.json`
(`depends_on`/`blocks`). An issue file just states its own ID so the graph and the file can be
cross-referenced.

Why split by domain: clear ownership (an agent only looks in its own domain folder plus
`integration/` for cross-cutting work), and it scales cleanly as more domains get added.
