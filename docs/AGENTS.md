# Agent Guidance

Use this repository with the Stage 1 documentation corpus.

## Where to Look
- Start with `docs/ARCHITECTURE.md` for subsystem overviews, then `docs/COMPONENTS.md` and `docs/ALGORITHMS.md` for specifics.
- `docs/_analysis` holds working notes (symbol map, execution flows, dependency notes). `docs/_generated/scratch_notes.md` contains raw external extractions.
- `docs/API_MAPPING.md` provides Java→Python translation hints. `docs/LEGACY_REFERENCES.md` links external docs.

## Workflow Tips
- Maintain the phase checklist in `docs/_analysis/agent_plan.md` when adding analyses.
- Update `_analysis` files before refining top-level docs; keep uncertainties explicit.
- Respect package scopes when editing code; avoid large refactors without explicit planning.

## Prompting Patterns
- **Architecture Q&A**: reference `ARCHITECTURE.md` and `COMPONENTS.md`, cite files.
- **Algorithm explanation**: use `ALGORITHMS.md` and source snippets, add citations.
- **Python translation**: consult `API_MAPPING.md`, propose module/class mirrors and dependency replacements.
- **External docs**: sync with `LEGACY_REFERENCES.md`; update `EXTERNAL_SOURCES.md` statuses.

## Testing Expectations
- For documentation-only changes note “not run” tests. For code changes, run relevant unit/integration checks when feasible.
