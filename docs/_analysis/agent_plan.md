# Agent Plan

This file tracks Stage 1 documentation work for the TrakEM2 repository. Each phase is described with its status and pending actions.

## Phase Checklist
- [x] Phase 0 — Initial Repository Reconnaissance: repository layout reviewed and initial notes captured.
- [x] Phase 1 — Symbol Extraction & Package Map: packages enumerated; symbol map drafted in `docs/_analysis/symbol_map.md`.
- [x] Phase 2 — Architecture Reconstruction: subsystems and flows documented in `docs/ARCHITECTURE.md` and `_analysis/execution_flows.md`.
- [x] Phase 3 — Component & Algorithm Documentation: components/algorithms summarized in `docs/COMPONENTS.md` and `docs/ALGORITHMS.md`.
- [x] Phase 4 — API Mapping and Future Python Bridge: initial mapping captured in `docs/API_MAPPING.md`.
- [x] Phase 5 — Agent Navigation & Interaction Guidance: see `docs/AGENTS.md` and `docs/VSCODE_AGENT_WORKFLOW.md`.
- [x] Phase 6 — External Documentation Integration: plugin page/manual summarized in `docs/LEGACY_REFERENCES.md`; wiki/javadoc pending.
- [x] Phase 7 — Refinement Cycles: first refinement pass completed; further deep dives noted below.

## Phase 0 Summary (Repository Reconnaissance)
- Root contents: `README.md`, `LICENSE.txt`, `TODO`, `TODO.ElasticAlign`, Maven `pom.xml`, `docs/`, `src/`, `test/`.
- Java sources under `src/main/java/` with top-level packages: `bunwarpj`, `ini`, `lenscorrection`, `mpi`, `mpicbg`, `org`.
- Resources under `src/main/resources/` (not yet inspected).
- Test and script assets under `test/` (Java test plus several scripting files: BeanShell, JS, CLJ).
- Existing documentation: `docs/EXTERNAL_SOURCES.md` describing external references to integrate later; now complemented by Stage 1 docs.

## Notes and Next Steps
- Deepen algorithm parameter documentation (alignment, lens correction) by inspecting mpicbg pipelines and filter defaults.
- Revisit concurrency in `mpicbg.trakem2.align.concurrent` and `ini.trakem2.parallel`.
- Fetch GitHub wiki and Fiji javadoc content when accessible; reconcile with current docs and update `LEGACY_REFERENCES.md`/`scratch_notes`.
- Consider adding diagrams once scope is clearer and tooling is available.
