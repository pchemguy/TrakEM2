# VSCode Agent Workflow

1. **Read scope files**: check for `AGENTS.md` (repo root/doc scopes). Current guidance lives in `docs/AGENTS.md`.
2. **Consult plan**: review `docs/_analysis/agent_plan.md` and `docs/DOCUMENTATION_PLAN.md` for phase status and TODOs.
3. **Prepare edits**: update `_analysis` notes first (symbol maps, flows), then propagate to top-level docs.
4. **Run checks**: for documentation-only changes, note that tests were not run; for code edits, run targeted Maven tests when feasible.
5. **Citations**: when responding, cite file paths using provided format.
6. **PR prep**: summarize changes, include command outputs for tests, and use `make_pr` after committing.
