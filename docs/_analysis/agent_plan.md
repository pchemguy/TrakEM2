# Agent Plan

This file tracks Stage 1 documentation work for the TrakEM2 repository. Each phase is described with its status and pending actions.

## Phase Checklist
- [x] Phase 0 — Initial Repository Reconnaissance: repository layout reviewed and initial notes captured.
- [ ] Phase 1 — Symbol Extraction & Package Map: enumerate packages/classes and draft symbol map.
- [ ] Phase 2 — Architecture Reconstruction: analyze core components and data flows; populate execution flows.
- [ ] Phase 3 — Component & Algorithm Documentation: summarize components and algorithm-heavy areas.
- [ ] Phase 4 — API Mapping and Future Python Bridge: outline Java APIs and Pythonic equivalents.
- [ ] Phase 5 — Agent Navigation & Interaction Guidance: guidance for future LLM agents.
- [ ] Phase 6 — External Documentation Integration: process sources from `docs/EXTERNAL_SOURCES.md`.
- [ ] Phase 7 — Refinement Cycles: tighten documentation and resolve gaps.

## Phase 0 Summary (Repository Reconnaissance)
- Root contents: `README.md`, `LICENSE.txt`, `TODO`, `TODO.ElasticAlign`, Maven `pom.xml`, `docs/`, `src/`, `test/`.
- Java sources under `src/main/java/` with top-level packages: `bunwarpj`, `ini`, `lenscorrection`, `mpi`, `mpicbg`, `org`.
- Resources under `src/main/resources/` (not yet inspected).
- Test and script assets under `test/` (Java test plus several scripting files: BeanShell, JS, CLJ).
- Existing documentation: `docs/EXTERNAL_SOURCES.md` describing external references to integrate later; no internal architecture docs yet.

## Immediate Next Steps
1. Begin Phase 1 by enumerating packages/classes to build `docs/_analysis/symbol_map.md`.
2. Create placeholder documentation files under `docs/` as required by Stage 1 (e.g., ARCHITECTURE, COMPONENTS) and note scopes.
3. Inspect `src/main/java` packages to identify subsystem boundaries for later phases.
